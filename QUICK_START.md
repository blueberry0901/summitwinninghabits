# Quick Start Guide - Adding Your AWS Video

## Quick Steps

### 1. Get Your AWS Video URL

You need to get a publicly accessible URL for your video. Here are the fastest methods:

#### Option A: Quick S3 Public URL
```bash
# Upload video
aws s3 cp your-video.mp4 s3://your-bucket/videos/

# Make bucket public (in S3 Console)
# Then use this URL format:
https://your-bucket.s3.region.amazonaws.com/videos/your-video.mp4
```

#### Option B: CloudFront URL (Recommended for large files)
```bash
# Upload video to S3
aws s3 cp your-video.mp4 s3://your-bucket/videos/

# In AWS Console: Create CloudFront distribution pointing to S3 bucket
# Wait ~15 minutes, then use:
https://d1234567890.cloudfront.net/videos/your-video.mp4
```

### 2. Add Video URL to Website

Open `video.html` and find this line:
```html
<source src="https://your-bucket-name.s3.your-region.amazonaws.com/path/to/video.mp4" type="video/mp4">
```

Replace with your actual URL:
```html
<source src="https://your-bucket.s3.us-east-1.amazonaws.com/videos/your-video.mp4" type="video/mp4">
```

### 3. Test It

Open `video.html` in a browser to see if your video loads.

---

## Detailed Instructions

For detailed setup, security options, troubleshooting, and cost considerations, see:
- **[AWS_VIDEO_SETUP.md](./AWS_VIDEO_SETUP.md)** - Complete AWS setup guide

## Common Issues

**Video won't play?**
- Check the URL works by opening it directly in a browser
- Make sure CORS is configured if needed
- Verify the video format is MP4 (H.264 codec recommended)

**Getting errors?**
- See troubleshooting section in AWS_VIDEO_SETUP.md
- Check browser console for error messages

**Want private videos?**
- Use CloudFront signed URLs (see AWS_VIDEO_SETUP.md Method 2)
- Or use presigned URLs (see AWS_VIDEO_SETUP.md Method 3)

---

## That's It!

Once you replace the placeholder URL with your actual AWS video URL, your video will be live on your website.

