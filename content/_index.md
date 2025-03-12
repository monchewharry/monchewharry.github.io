---
title: My page
type: landing
design:
  # Default section spacing
  spacing: "0rem"
sections:
  - block: biography
    content:
      username: admin
      button:
        text: Download Résumé
        url: /uploads/resumeEN.pdf
    design:
      banner:
        # Upload your cover image to the `assets/media/` folder and reference it here
        filename: shanghai_1.jpg
      biography:
        # Customize the style of your biography text
        style: 'text-align: justify; font-size: 0.8em;'
  - block: collection
    content:
      title: "Recent Posts"
      filters:
        folders:
          - blogs
    design:
      spacing:
        padding: ['3rem', 0, '6rem', 0]
---
