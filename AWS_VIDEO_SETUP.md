# AWS Video Setup Guide

This guide explains how to set up AWS to host your videos and integrate them into the website.

## Overview

There are three main methods to host videos on AWS:

1. **Direct S3 URL** - Simple but requires public bucket
2. **CloudFront** - Best performance and lower costs
3. **Presigned URLs** - Secure private videos with expiration

---

## Method 1: Direct S3 URL (Public Bucket)

### Pros
- Simple setup
- No additional AWS services needed
- Immediate availability

### Cons
- Video files are publicly accessible
- Higher bandwidth costs for large files
- Slower for viewers far from the S3 region

### Steps:

1. **Upload video to S3**
   ```bash
   # Using AWS CLI
   aws s3 cp your-video.mp4 s3://your-bucket-name/videos/
   ```

2. **Make the bucket public**
   - Go to S3 Console → Your Bucket → Permissions
   - Edit "Block public access" settings
   - Uncheck "Block all public access" and save
   - Add this bucket policy:
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "PublicReadGetObject",
         "Effect": "Allow",
         "Principal": "*",
         "Action": "s3:GetObject",
         "Resource": "arn:aws:s3:::your-bucket-name/*"
       }
     ]
   }
   ```

3. **Get the video URL**
   ```
   https://your-bucket-name.s3.your-region.amazonaws.com/videos/your-video.mp4
   ```

4. **Add to HTML**
   ```html
   <source src="https://your-bucket-name.s3.us-east-1.amazonaws.com/videos/your-video.mp4" type="video/mp4">
   ```

---

## Method 2: CloudFront (Recommended)

### Pros
- Better performance (CDN caching)
- Lower bandwidth costs
- Better for global audience
- Can restrict access if needed

### Cons
- Slightly more complex setup
- Additional AWS service

### Steps:

1. **Upload video to S3** (can be private)
   ```bash
   aws s3 cp your-video.mp4 s3://your-bucket-name/videos/
   ```

2. **Create CloudFront Distribution**
   - Go to CloudFront Console → Create Distribution
   - Origin Domain: Select your S3 bucket
   - Viewer Protocol Policy: Redirect HTTP to HTTPS
   - Price Class: Choose based on your needs
   - Create Distribution

3. **Wait for deployment** (15-30 minutes)

4. **Get CloudFront URL**
   ```
   https://d1234567890.cloudfront.net/videos/your-video.mp4
   ```

5. **Add to HTML**
   ```html
   <source src="https://d1234567890.cloudfront.net/videos/your-video.mp4" type="video/mp4">
   ```

### Optional: Restrict Access with Signed URLs

For private videos through CloudFront:

1. **Create CloudFront Key Pair**
   - Go to CloudFront → Public Keys → Create
   - Create Public Key and Private Key
   - Save the Private Key securely

2. **Create Distribution with Signed URLs**
   - Edit CloudFront Distribution
   - Default Cache Behavior → Restrict Viewer Access: Yes
   - Trusted Signers: Self

3. **Generate Signed URL** (use AWS SDK or CLI)
   ```python
   # Python example
   from datetime import datetime, timedelta
   from cryptography.hazmat.primitives import hashes, serialization
   from cryptography.hazmat.backends import default_backend
   import base64, json

   # Your CloudFront URL
   url = "https://d1234567890.cloudfront.net/videos/your-video.mp4"
   
   # Expiration (e.g., 7 days from now)
   expiration = datetime.utcnow() + timedelta(days=7)
   policy = {
       "Statement": [{
           "Resource": url,
           "Condition": {"DateLessThan": {"AWS:EpochTime": int(expiration.timestamp())}}
       }]
   }
   
   # Sign and create URL (requires your private key)
   # This is complex - consider using AWS SDK or CloudFront CLI
   ```

---

## Method 3: Presigned URLs (Private Videos)

### Pros
- Secure - videos not publicly accessible
- Time-limited access
- No CloudFront setup needed
- Good for temporary access

### Cons
- URLs expire after set time
- Need to regenerate URLs periodically
- More complex implementation

### Steps:

1. **Upload video to S3** (private bucket)

2. **Generate Presigned URL** (expires in 7 days example)
   ```python
   # Python with boto3
   import boto3
   from datetime import timedelta
   
   s3_client = boto3.client('s3')
   
   url = s3_client.generate_presigned_url(
       'get_object',
       Params={
           'Bucket': 'your-bucket-name',
           'Key': 'videos/your-video.mp4'
       },
       ExpiresIn=604800  # 7 days in seconds
   )
   
   print(url)
   ```

   Or with AWS CLI:
   ```bash
   aws s3 presign s3://your-bucket-name/videos/your-video.mp4 --expires-in 604800
   ```

3. **Add to HTML**
   ```html
   <source src="YOUR_GENERATED_PRESIGNED_URL" type="video/mp4">
   ```

**Note:** Presigned URLs expire, so you'll need to regenerate them periodically. For static websites, consider using CloudFront with a longer expiration time or automatic URL generation.

---

## Video Format Recommendations

### Supported Video Formats
- **MP4** (H.264 codec) - Most compatible
- **WebM** (VP9 codec) - Good quality, smaller files
- **OGG** - Less common

### Best Practices
```html
<!-- Provide multiple formats for maximum compatibility -->
<video controls width="100%">
    <source src="video.mp4" type="video/mp4">
    <source src="video.webm" type="video/webm">
    Your browser does not support the video tag.
</video>
```

### Recommended Settings for Long Videos
- **Codec**: H.264 (MP4)
- **Resolution**: 1080p (1920x1080) or 720p (1280x720)
- **Bitrate**: 5-8 Mbps for 1080p, 2-4 Mbps for 720p
- **Audio**: AAC codec, 128 kbps

---

## Cost Estimation

### S3 Storage
- ~$0.023 per GB/month

### S3 Data Transfer (Direct)
- First 1 TB: $0.09 per GB
- Next 40 TB: $0.085 per GB

### CloudFront Data Transfer
- First 10 TB: $0.085 per GB
- Next 40 TB: $0.080 per GB
- Much cheaper than S3 direct transfer!

**Example:** A 1GB video viewed 1000 times:
- S3 Direct: ~$90 in bandwidth
- CloudFront: ~$85 in bandwidth
- Savings increase with higher volumes and global distribution

---

## Security Considerations

### For Internal Use Only
- Use CloudFront with Origin Access Identity (OAI)
- Implement signed URLs or cookies
- Consider IP restrictions
- Use IAM policies for S3 access

### For Public Videos
- Keep S3 bucket private
- Use CloudFront with Origin Access Control (OAC)
- Enable CloudFront logging for monitoring

---

## Troubleshooting

### Video Not Playing
1. **Check CORS settings** (if loading from different domain):
   ```json
   [
     {
       "AllowedHeaders": ["*"],
       "AllowedMethods": ["GET", "HEAD"],
       "AllowedOrigins": ["https://your-website.github.io"],
       "ExposeHeaders": ["Content-Length", "Content-Range"],
       "MaxAgeSeconds": 3000
     }
   ]
   ```

2. **Verify URL is accessible** - Open in browser directly
3. **Check video format** - Browser might not support the codec
4. **Check file permissions** - Ensure proper ACLs set

### Slow Loading
- Use CloudFront instead of direct S3
- Enable CloudFront compression
- Use adaptive bitrate streaming (more complex setup)

### High Costs
- Move to CloudFront for lower bandwidth costs
- Enable CloudFront caching headers
- Consider compression/transcoding to lower bitrates

---

## Next Steps

1. Choose your method based on requirements
2. Set up your AWS resources
3. Upload your video
4. Get the URL
5. Replace the placeholder URLs in the HTML files
6. Test on your website

For production use, **CloudFront is recommended** for better performance and lower costs.

