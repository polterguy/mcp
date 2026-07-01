---
name: background-images
when_to_use: When building a frontend or landing page and the user needs a background image, or asks which background images are available.
---

# Background images for web apps

Magic ships with a library of background images in its `/misc/images/` folder you can use when creating frontends or landing pages.

1. Use the `list-files` tool to list all files in `/misc/images/`, then show the user what's available and ask whether they'd like to use one as a background image.
2. If the user confirms, use the `copy-file` tool to copy the chosen file from `/misc/images/` to, for instance, `/etc/www/assets/`, then reference it as a background image in your generated frontend.
