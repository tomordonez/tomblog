---
layout: post
title: "Power BI Design Techniques"
author: tom
tags: [analytics, data visualization, powerbi]
comments: true
published: false
---

Power BI design settings for metrics, bar charts, maps, and other visuals.

## Layout Techniques

**Color Palette**

* Explore trending color palettes using [Coolors](https://coolors.co/palettes/trending)
* Pick a palette and copy/paste the color codes to the report/visuals

**Align/Resize visuals**

* Select multiple visuals that need to be aligned
* Go to `Visualizations/Format visual/General/Properties`
* Update any of these settings: Size, Position, Padding

**Whitespace hack for padding**

* I wish PBI visuals had a similar behavior as the Box Model in CSS with padding, border, and margin
* Create a shape of the same background color as the visual
  * For example: `Insert/Shapes/Rectangle`
  * `Resize` the shape to the same size as the visual
    * Add 20px to the shape's height and width `General/Properties/Size`
  * Add `rounded corners`
    * Select the visual, then `General/Effects`
    * Set `Background` to `On`
      * Set same color as the shape's background
    * Set `Visual Border` to `On`
      * Set same color as the shape's background
      * Set `Rounded corners` to `15px`
  * Add `shadow`
    * Set `Shadow` to `On`
      * Change the color to a ligher setting than the default
      * Set `Position` to `Custom`
        * Set `Transparency` to `90px`
* Organize the layers so the shape is behind the visual
  * `View/Selection/Layer order`
    * Rename the layer with a proper name
  * Or use `Format/Send backward` (when selecting the visual)
* Make sure the shape's color is the same as the visual's background
* Turn shape's border to off

![PowerBI Design Whitespace Padding](/assets/images/powerbi-design-whitespace-padding.png)

**Format painter to copy/paste a format**

* Click on a visual
* Under the Home menu tab
* Click `format painter`
* Use paintbrush mouse pointer to click on a similar visual
* This will copy/paste the format to that visual

## Gradient Blue Background

If you want to create a background with a gradient to have better contrast and a modern design, you can use an external image and use it as a background.

![PowerBI and Azure Topics in Data Analytics](/assets/images/powerbi-azure-topics-data-analytics-dashboard.png)

More about this report in [Power BI with Azure and Data Analytics Trends](../powerbi-azure-data-analytics-trends/)

**Power BI Canvas Settings**

* Review the canvas size
* The default is type `16:9` (height: 720px, width: 1280px)

**Google Drive**
* Create a Google Slide
* Set the page setup to `16:9` or select `Custom` and enter the size in pixels
* Change the background
* Select `color`
* Go to the `Gradient` tab
* Add a custom color
* Select any preferred settings
* Download the slide as `.svg`

**Power BI Canvas background**

* Without clicking any visual
* Go to the menu Visualizations, then the tab `Format page`
* Click `Canvas background`
* Click on `Image` and upload the `svg` file
  * Set Image fit to `Fit`
  * Set `Transparency` to `0`

![Power BI Background Color Gradient](/assets/images/powerbi-background-color-gradient.png)

***

## Designs for Visuals

**Report Text Box Title**

* Header: Segoe UI, 18px, Bold
* Subheader/description: Segoe UI Light, 11px

![Power BI Design Visual Text Box](/assets/images/powerbi-design-visual-textbox.png)

**Multi-row Card**

* Header
  * Visualizations/General/Title
  * Heading: Heading 3
  * Font: Segoe UI Light, 12px
* Values
  * Visualizations/Visual/Callout values
  * Font: Segoe UI, 18px, Bold
* Labels
  * Visualizations/Visual/Category labels set to `On`
  * Font: Segoe UI Light, 11px, Bold

![Power BI Design Visual Multi-row Card](/assets/images/powerbi-design-visual-multicard.png)

**Stacked Bar Chart**

![Power BI Design Visual Stacked Bar Chart](/assets/images/powerbi-design-visual-stacked-bar-chart.png)

* Header
  * Visualizations/Format visual/General/Title
  * Heading: Heading 3
  * Font: Segoe UI, 14px, Bold, Left alignment
* Y-Axis, X-Axis
  * Visualizations/Format visual/Y-axis/Values
  * Font: Segoe UI, 9px
  * Max area width: 50%
  * Concatenate labels: ON
* Gridlines
  * Style: Dotted
  * Color: Light grey `#C8C6C4`
  * Width: 1px
* Bars/Colors
  * Conditional formatting
  * Format style: Gradient
  * What field based on: Sum of Views
  * Summarization: Sum
  * How to format empty values: As zero
  * Minimum: Lowest value
  * Maximum: Highest value
* Bars/Spacing
  * Inner padding: 20px
  * Min category width: 20px

![Power BI Design Visual Stacked Bar Chart Conditional Formatting](/assets/images/powerbi-design-visual-stacked-bar-chart-conditional-formatting.png)

**Make a slicer look like a website menu**

* Select the slicer
* Format visual/Visual
* Slicer settings
  * Options/Style set to `Tile`
* Values
  * Font/padding `4px`
* Border
  * Bottom
  * Set a color and line width like 10px
* Background
  * Set the same color as the top bar

![Power BI Visual Slicer Menu](/assets/images/powerbi-design-slicer-menu.png)


***

## Organize the Layers

As you add visuals to the report, you can organize them in layers, and name the visuals with a naming convention.

![Power BI Visual Layer Order](/assets/images/powerbi-design-visual-layer-order.png)

* Go to `View`
* Then `Selection`
* A vertical menu opens with two tabs `Layer order` and `Tab order`
* In `Layer order`, rename the visuals
* Drag/drop the visuals on top of each other
* Optionally, hide visuals
  * This `show/hide` can also be used when creating bookmarks
  * Create two visuals on the same canvas location
  * For example, `sales by year` and `sales by month`
  * Place the layers so they are exactly on top of each other on the canvas
  * When creating a bookmark hide one of the visuals and save it
  * Then swap show/hide for the other visual and create another bookmark

***

## Visual Tab Order

![Power BI Visual Tab Order](/assets/images/powerbi-design-visual-tab-order.png)

The adjacent tab says `Tab order` and it shows this message when you browse over:

    Choose the way people tab through your report elements and hear them read by a screen reader.

You can order visuals by drag/drop and choose to hide visuals from this tab browsing.

***

## Performance Analyzer

* Click on a new a report page
* Click on the Optimize menu tab
* Then `Performance Analyzer` and `Start recording`
* Go back to the report
* It shows the duration it takes to execute each layer
* Hidden visualization layers don't get loaded `View/Selection/Layer order`
* Expand any element in the analyzer to review duration of each element in a layer/visual

![Power BI Design Performance Analyzer](/assets/images/powerbi-design-performance-analyzer.png)

Microsoft has [this](https://learn.microsoft.com/en-us/power-bi/guidance/import-modeling-data-reduction) document on data reduction techniques, which in a way summarizes to this:

**From faster to slower**

Star schemas, measurements

* Use a DW at the source with data marts, star schemas, and measurement/summary/date tables
* Use Power Query M to create star schema, transformations, and measurement tables
* Use DAX to create tables and measurements

Storage mode, importing data

* Set Storage Mode to DirectQuery and a SQL script to load only what is required
  * Large datasets, real time analytics
* Set Storage Mode to Mixed Mode and a SQL script
* Set Storage Mode to Import and a SQL script

**Set Storage mode per table**

* Go to `Model view`
* Select a table
* Go to `Advanced`
* Select `Storage mode`
  * You can switch from DirectQuery to Import
  * You cannot switch from Import to DirectQuery

[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)