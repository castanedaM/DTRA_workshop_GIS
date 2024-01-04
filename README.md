[![01 Build and Deploy Site](https://github.com/datacarpentry/r-raster-vector-geospatial/actions/workflows/sandpaper-main.yaml/badge.svg)](https://github.com/datacarpentry/r-raster-vector-geospatial/actions/workflows/sandpaper-main.yaml)

# DTRA WORKSHOP: R for Raster and Vector Data

## Contributing to lesson development

- The lesson files to be edited are in the `_episodes` folder. This repository uses the `main` branch for development.
- You can visualize the changes locally with the [sandpaper](https://github.com/carpentries/sandpaper) R package by executing either the `sandpaper::serve()` or `sandpaper::build_lesson()` commands. In the former case, the site will be rendered at [http://localhost:4321](http://localhost:4321)
- Each time you push a change to GitHub, Github Actions rebuilds the lesson, and when it's successful (look for the green badge at the top of the README file), it publishes the result at [http://www.datacarpentry.org/r-raster-vector-geospatial/](http://www.datacarpentry.org/r-raster-vector-geospatial/)
- Note: any manual commit to `gh-pages` will be erased and lost during the automated build and deploy cycle operated by Github Actions.
