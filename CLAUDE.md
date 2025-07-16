# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Hugo-based static site generator project for Pavel Anni's personal website (pavelanni.dev). It's a blog and portfolio site featuring technical content, projects, and personal texts. The site is built with Hugo and deployed via Cloudflare.

## Setup Instructions

### Fresh Repository Clone
When cloning this repository for the first time, follow these steps:

1. **Clone the repository:**
   ```bash
   git clone https://github.com/pavelanni/pavelanni-dev.git
   cd pavelanni-dev
   ```

2. **Initialize and update git submodules:**
   ```bash
   git submodule update --init --recursive
   ```
   This is **required** because the PaperMod theme is included as a git submodule.

3. **Install Hugo:**
   - Make sure you have Hugo extended version installed (v0.140.0 or later recommended)
   - On macOS: `brew install hugo`
   - On other platforms: See [Hugo installation guide](https://gohugo.io/installation/)

4. **Test the setup:**
   ```bash
   hugo server
   ```
   The site should be available at http://localhost:1313/

### Common Issues
- **404 Page not found**: Usually means the theme submodule wasn't initialized. Run `git submodule update --init --recursive`
- **Template errors**: Make sure you have Hugo extended version and the theme is up to date
- **Build failures**: Check Hugo version compatibility and ensure all submodules are properly initialized

## Development Commands

### Hugo Commands
- `hugo server` - Start development server with live reload
- `hugo server -D` - Start development server including draft content
- `hugo` - Build the static site to `/public` directory
- `hugo new content/blog/post-name/index.md` - Create new blog post
- `hugo new content/projects/project-name/index.md` - Create new project page
- `hugo new content/texts/text-name/index.md` - Create new text page

### Site Management
- The site is deployed automatically via Cloudflare
- All content is in Markdown format with YAML frontmatter
- Images should be placed in the same directory as the content file using `relative: true`

## Content Architecture

### Content Types
The site has three main content sections:

1. **Blog** (`/content/blog/`) - Technical articles and stories
2. **Projects** (`/content/projects/`) - Portfolio projects with detailed technical information
3. **Texts** (`/content/texts/`) - Essays, thoughts, and longer-form content

### Content Structure
- Each content item lives in its own directory with an `index.md` file
- Images are stored alongside the content using Hugo's page bundles
- Cross-references between blog posts and projects are handled via frontmatter links

### Frontmatter Schema

#### Blog Posts
```yaml
title: "Post Title"
date: "2024-01-01"
draft: false
description: "Brief description"
cover:
  image: "image.webp"
  relative: true
project: "/projects/project-name"  # Optional link to related project
```

#### Projects
```yaml
title: "Project Name"
date: "2024-01-01"
lastmod: "2024-01-02"
draft: false
description: "Project description"
cover:
  image: "cover.webp"
  relative: true
status: "active"  # active, completed, archived
platform:
  - "Platform 1"
  - "Platform 2"
technologies:
  - "Technology 1"
  - "Technology 2"
links:
  github: "https://github.com/user/repo"
  story: "/blog/related-story"
```

## Theme and Layout System

### PaperMod Theme
The site uses a customized PaperMod theme located in `/themes/PaperMod/`. Custom modifications are made through:
- Layout overrides in `/layouts/`
- CSS extensions in `/assets/css/extended/`
- Partial overrides in `/layouts/partials/`

### Custom Components

#### Home Page (`/layouts/_index.html`)
- Custom homepage layout showing recent projects and blog posts
- Displays 3 latest projects with status indicators
- Shows 5 most recent blog posts and texts

#### Project Pages (`/layouts/projects/single.html`)
- Special project metadata display including status, platforms, and technologies
- Project status badges (active, completed, archived)
- Technology and platform tags
- Links to GitHub repos and related blog stories

#### Blog Pages (`/layouts/blog/single.html`)
- Standard blog post layout with breadcrumbs
- Optional project reference links
- Reading time and word count display

### Styling System
- Base theme: PaperMod
- Custom CSS in `/assets/css/extended/`:
  - `home.css` - Homepage styling
  - `project-card.css` - Project-specific styling
  - `blog-project.css` - Blog post styling
- Color scheme uses CSS custom properties for theming
- Project status indicators with color coding

## Configuration

### Site Configuration (`hugo.yaml`)
- Base URL: https://pavelanni.dev/
- Theme: PaperMod
- Main sections: blog, projects, texts
- Social links configured in params
- Menu structure with weighted navigation

### Analytics
- Google Analytics integration via custom head partial
- Tracking ID: G-VFKTFN0DW8

## Content Guidelines

### Writing Style
- Technical content focuses on learning experiences and practical implementation
- Project documentation includes both technical details and narrative storytelling
- Personal voice and approachable tone throughout

### Image Management
- Use WebP format for optimal loading
- Store images in content directories using page bundles
- Always use `relative: true` in frontmatter for local images
- Include alt text for accessibility

### Cross-Content Linking
- Blog posts can reference projects via `project:` frontmatter field
- Projects can link to related blog stories via `links.story:` field
- Use relative URLs for internal links

## Development Notes

### Content Organization
- Each major content item gets its own directory
- Images live alongside content for better organization
- Consistent naming: use lowercase with hyphens for directories

### Theme Customization
- Extend PaperMod through `/layouts/` overrides, not direct theme modification
- Custom CSS goes in `/assets/css/extended/`
- Maintain theme upgrade compatibility by avoiding direct theme edits

### Performance Considerations
- Use WebP images for better compression
- Leverage Hugo's built-in image processing if needed
- Keep custom CSS minimal and efficient