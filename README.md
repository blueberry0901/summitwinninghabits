# Organization Internal Website

A simple and professional internal website for organizational information, built for deployment on GitHub Pages.

## Features

- **Clean Navigation**: Easy-to-use navigation bar with five main sections
- **Responsive Design**: Works seamlessly on desktop, tablet, and mobile devices
- **Professional Styling**: Modern, clean interface with subtle animations
- **Video Support**: Embedded video player for AWS-hosted videos
- **Four Main Sections**:
  - Team Introduction: Showcase your organization's team members
  - Provider Introduction: Display information about service providers and partners
  - License Introduction: List certifications, accreditations, and licenses
  - Video: Host and display videos from AWS S3 or CloudFront

## File Structure

```
/
├── index.html          # Home page
├── team.html           # Team Introduction page
├── providers.html      # Provider Introduction page
├── licenses.html       # License Introduction page
├── video.html          # Video page
├── styles.css          # Main stylesheet
├── AWS_VIDEO_SETUP.md  # AWS video hosting guide
└── README.md           # This file
```

## Deployment to GitHub Pages

### Steps:

1. **Create a GitHub Repository**
   - Create a new repository on GitHub
   - Clone it to your local machine

2. **Add Your Files**
   ```bash
   git clone <your-repo-url>
   cd <your-repo-name>
   # Copy all website files to this directory
   git add .
   git commit -m "Initial commit"
   git push origin main
   ```

3. **Enable GitHub Pages**
   - Go to your repository on GitHub
   - Navigate to Settings → Pages
   - Under "Source", select "Deploy from a branch"
   - Choose "main" branch and "/ (root)" folder
   - Click "Save"

4. **Access Your Website**
   - Your site will be available at: `https://<your-username>.github.io/<repo-name>`
   - GitHub Pages typically takes a few minutes to deploy

## Customization

### Updating Content

1. **Team Information**: Edit `team.html` to add/remove team members
2. **Provider Information**: Edit `providers.html` to update provider details
3. **License Information**: Edit `licenses.html` to add certifications/licenses
4. **Home Page**: Edit `index.html` to customize the landing page
5. **Video Content**: Edit `video.html` to add your AWS video URLs (see AWS_VIDEO_SETUP.md)

### Styling

- Modify `styles.css` to change colors, fonts, spacing, etc.
- Main color scheme uses:
  - Primary: `#2c3e50` (dark blue-gray)
  - Accent: `#3498db` (blue)
  - Success: `#2ecc71` (green)
  - Text: `#333` (dark gray)
  - Light text: `#7f8c8d` (gray)

### Adding Images

To add profile pictures or logos:
1. Create an `images` folder in your repository
2. Add your image files there
3. Update the HTML to reference them: `<img src="images/photo.jpg" alt="Description">`

### Adding Videos

To add videos from AWS:
1. Upload your video to AWS S3 or CloudFront
2. See `AWS_VIDEO_SETUP.md` for detailed instructions
3. Get your video URL (S3, CloudFront, or presigned URL)
4. Replace the placeholder URL in `video.html` or `index.html`
5. Update the `<source>` tag's `src` attribute with your URL

## Browser Support

- Chrome (latest)
- Firefox (latest)
- Safari (latest)
- Edge (latest)
- Mobile browsers (iOS Safari, Chrome Mobile)

## License

This project is available for internal use. Customize as needed for your organization.

