# 📊 Dashboards

A collection of standalone HTML dashboards published via GitHub Pages. Each dashboard is a self-contained HTML file with inlined CSS and JavaScript for easy deployment and sharing.

## 🌐 Live Site

Visit the live dashboards at: `https://<username>.github.io/dashboards/`

## 📁 Structure

```
dashboards/
├── index.html              # Landing page with dashboard listing
├── dashboards/             # Directory containing individual dashboards
│   └── sample-dashboard.html
├── .github/
│   └── workflows/
│       └── deploy.yml      # GitHub Pages deployment workflow
└── .nojekyll              # Prevents Jekyll processing
```

## 🚀 Adding New Dashboards

1. Create a new HTML file in the `dashboards/` directory
2. Ensure all CSS and JavaScript are inlined within the HTML file
3. Update the `dashboards` array in `index.html` to include your new dashboard:

```javascript
const dashboards = [
    {
        name: "Your Dashboard Name",
        file: "dashboards/your-dashboard.html",
        description: "Description of your dashboard"
    }
];
```

4. Commit and push your changes - GitHub Actions will automatically deploy

## 📝 Dashboard Template

Each dashboard should be a complete HTML file with:
- Inlined CSS in `<style>` tags
- Inlined JavaScript in `<script>` tags
- No external dependencies (unless absolutely necessary)
- Responsive design for mobile and desktop viewing
- A link back to the main index page

See `dashboards/sample-dashboard.html` for a complete example.

## 🔧 Local Development

To preview dashboards locally:

```bash
# Using Python
python -m http.server 8000

# Using Node.js
npx http-server

# Using PHP
php -S localhost:8000
```

Then open `http://localhost:8000` in your browser.

## 📦 Deployment

Dashboards are automatically deployed to GitHub Pages when changes are pushed to the `main` branch using GitHub Actions.

### Manual Deployment

If needed, you can trigger a manual deployment:
1. Go to the "Actions" tab in your GitHub repository
2. Select the "Deploy to GitHub Pages" workflow
3. Click "Run workflow"

## 🎨 Features

- **Self-contained**: Each dashboard is a single HTML file
- **No build step**: Pure HTML, CSS, and JavaScript
- **Fast loading**: No external dependencies to fetch
- **Easy sharing**: Just share the URL to any dashboard
- **Responsive**: Works on mobile and desktop devices

## 📄 License

This project is open source and available under the MIT License.