---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Soundscape Dashboard"
summary: "This dashboard shows the sound 'library' of Sequoia and Kings Canyon National Parks"
authors: [Joshua Flickinger]
tags: [nps, soundscape, ESRI, ArcGIS Online, dashboard]
categories: [dashboard]
date: 2020-05-30T09:20:53-07:00

# Optional external URL for project (replaces project detail page).
external_link: ""

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: "[Click here to visit the dashboard.](https://nps.maps.arcgis.com/apps/opsdashboard/index.html#/c949424576814662a7af816932376313)"
  focal_point: ""
  preview_only: false

# Custom links (optional).
#   Uncomment and edit lines below to show custom links.
links:
  - name: See Live
    url: https://nps.maps.arcgis.com/apps/opsdashboard/index.html#/c949424576814662a7af816932376313
    icon_pack: fas
    icon: globe-americas

url_code: ""
url_pdf: ""
url_slides: ""
url_video: ""

# Slides (optional).
#   Associate this project with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides = "example-slides"` references `content/slides/example-slides.md`.
#   Otherwise, set `slides = ""`.
slides: ""
---

The Sequoia and Kings Canyon National Parks sound library houses a record of the biodiversity found in the southern Sierra Nevada.  Scientists, rangers, and story tellers have recorded sounds throughout the parks for a variety of projects.I created an [ESRI 'dashboard'](https://www.esri.com/en-us/arcgis/products/arcgis-dashboards/overview) to represent these sounds spatially and make them more widely available to virtual park visitors.  The dashboard is configured to present the sounds in an alphabetical list and filter sounds according to desired parameters, including elevation, ecoregion, type of sound, time of day, and media type.

Under the hood, this web mapping application is powered by an ArcGIS Online feature service that stores sound locations and associated attributes.  Audio clips are embedded in the feature service pop-ups via a link to their hosted location on Soundcloud.  Icons for each location were designed using the SVG editor Inkscape.

You can visit the dashboard [here](https://nps.maps.arcgis.com/apps/opsdashboard/index.html#/c949424576814662a7af816932376313).
