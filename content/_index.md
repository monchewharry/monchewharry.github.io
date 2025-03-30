---
title: My page
type: landing
design:
  # Default section spacing
  spacing: "0rem"
sections:
  - block: hero
    content:
      title: 👋 Hello
      text: Welcome to my page!
      image:
        # Reference an image in your `assets/media/` folder
        filename: avatar.jpg
      primary_action:
        text: Résumé
        url: /resume
        icon: rocket-launch
      secondary_action:
        text: Posts
        url: /post
      # announcement:
      #   text: "Announcing the release of version 1."
      #   link:
      #     text: "Read more"
      #     url: "/blog/"
    design:
      spacing:
        padding: [0, 0, 0, 0]
        margin: [0, 0, 0, 0]
      # For full-screen, add `min-h-screen` below
      css_class: "dark min-h-screen"
      background:
        color: "navy"
        image:
          # Add your image background to `assets/media/`.
          filename: shanghai_1.jpg
          filters:
            brightness: 0.5
---
