# CANgage

CANgage displays CAN data in real-time on a mini display, meant to be placed near the driver's line of sight like on the steering column or dash. It is highly configurable to the user's own requirements. Multiple pages can be configured and scrolled between, each showing different data via a variety of widgets.

![scr](https://github.com/IndyJoeA/CANgage/assets/29404417/ab8162f2-2e55-468e-ba47-f69841352bdf)

## Requirements

- **CANserver**: A device that connects to a vehicle's canbus and sends decoded data over wifi.
    - More about CANserver: http://www.jwardell.com/canserver
    - Firmware 2.2 is required on the CANserver to support UDP communication. To receive this version you will have to switch your CANserver Release Channel to Development and then have it check for updates to install the latest version.
- **Display Module**: Many ESP32-based display modules will ultimately be supported, firmware is currently available for the following. *NOTE*: The following boards also have touch-screen variants available. Touch control is not currently implemented, but the touch variants will still work as display only devices.
    - LilyGo T-Display-S3 AMOLED: https://www.lilygo.cc/products/t-display-s3-amoled
    - LilyGo T-Display-S3 LCD: https://www.lilygo.cc/products/t-display-s3
	
Feel free to join the [CANserver Discord](https://discord.gg/cuvMjw5tVh) and the channel *#displays-apps-accessories* for discussion and the latest updates on development.

## Firmware Installation

To install CANgage, a firmware file from the [Releases](https://github.com/IndyJoeA/CANgage/releases) page must be flashed to the device. 

### New firmware installation

If the firmware is being flashed to a device that does not currently have a version of CANgage installed, you will have to flash the binary file CANgage-*HardwareType*-USB-Factory.bin, where *HardwareType* must match the device you have. This binary file must be written to address 0x0000 and will overwrite the entire flash contents of the device, so it's not recommended to be used for upgrading versions. There are several utilities that can write firmware to ESP32-based devices, but here is a list of those that have been proven to work with CANgage:
- **ESPWebTool**: https://esp.huhn.me/
    - Online flash tool, no download needed. Using this tool will require putting the device into programming mode (hold the boot button while quickly pressing the reset button, then release the boot button). Also make sure to change the address to 0x0000.

### Upgrading firmware

If your device already has CANgage installed, you can update its firmware from the Status page. The file CANgage-*HardwareType*-OTA.zip is recommended as it contains both firmware and the WebUI files.

The firmware file options match those of the CANserver. More can be learned about these options from the [CANserver manual](https://github.com/joshwardell/CANserver/wiki/CANserverManual#upgrading).

## Configuring CANgage

The device running CANgage must be in proximity to the CANserver so that it can connect to its wifi network. Once the CANgage is powered on, it will automatically connect to the CANserver. Browse to the CANserver's web config page located at http://canserver.local, then from the Network tab, you should see an entry for CANgage in the Clients list. Click the gear icon to be re-directed to CANgage's web configuration page.

## Status page

The first tab is Status, and this displays the state of CANgage. You can also upload new firmware for CANgage at the bottom of this page.

![status](https://github.com/IndyJoeA/CANgage/assets/29404417/b2fc8323-f699-4c5b-90d6-2fdaf7203a69)

### Backup

Near the bottom of the Status page is a Backup button. This will download a CANgage-*HardwareType*-OTA-Data.bin file that serves as a complete backup of all display configuration and files that have been uploaded to CANgage. This file can be restored to the same device or a different device by using the [Update](#upgrading-firmware) feature and selecting this bin file.

## Display page

The Display page is where all display related settings are configured. On this page are two sections, the [Global Elements](#global-elements) section and the [Tiles](#tiles) section. Read below for details on how to use these sections to create your configuration.

![display](https://github.com/IndyJoeA/CANgage/assets/29404417/9968c10d-842d-4b65-ae80-9c5e9b3bbc0e)

### Data settings

Many items in the configuration have a *Data Type* and *Data Name* setting. This allows you to select what data from the CANserver will be displayed by each item. From the *Data Type* dropbown box, select *Analysis* to pick from the CANserver's Analysis Items, or *Lua* to pick from the CANserver's Lua scripting variables, and then select the specific data name from the *Data Name* dropdown. For more information about these data sources, see the CANserver manual sections on [Analysis Items](https://github.com/joshwardell/CANserver/wiki/CANServer-v2-Analysis) and [LUA Scripting](https://github.com/joshwardell/CANserver/wiki/CANServer-v2-Scripting).

### Settings editor

Most settings in the main configuration editor are required, with the exception of Description and Settings. 

Items can be moved up or down in the editor. Moving Widgets will move them before or after the other Widgets in the same row/column, and moving Tiles will place them before or after other Tiles and change their [index number](#tiles).

The Delete button will delete the item or Tile.

The Settings button will display a popup window with specific settings for that item, if available. The text to the left of the Settings button will show the word Default if no changes have been made, or a number indicating how many settings have been configured. All of these settings are optional. If they are not configured then the item will be displayed with its default configuration.

### Fonts

CANgage allows you to use a variety of built-in font sizes, or upload your own using the [Files page](#files-page). To use one of the built-in font sizes, leave the Font setting set to Default and select the desired size from the Font Size drop-down menu. If you have uploaded a custom font, select it from the Font drop-down menu and the Font Size menu will be disabled. Custom fonts are generated at a single font size per file, so if you want multiple sizes of the same font, you have to generate and upload the files individually.

#### Generating Custom Fonts

The file format used by CANgage for custom fonts is the [LVGL binary bitmap font format](https://github.com/lvgl/lv_font_conv/blob/master/doc/font_spec.md). These files can be generated using common font files like TTF and WOFF with one of the following converters. For CANgage, it's recommended to generate fonts at 2 BPP (bits per pixel) which will help keep the file sizes small and will generally look just as good as higher settings on the small screens typically used. It's also recommended to turn off the compression option because compression will slow down text rendering. These converters require a character range to be specified, generally what works best is 0x20 - 0x7E, since this is the standard ASCII range. If you use any symbols outside this range, like the degrees symbol for example, you have to include the character codes for those as well.

- **openHASP Online Font Converter**: https://www.openhasp.com/0.6.3/design/fonts/#binary
    - The easiest way to convert fonts. Font settings can be adjusted and TTF, OTF or WOFF files uploaded, at which point the binary file will be generated and downloaded.
- **lv_font_conv**: https://github.com/lvgl/lv_font_conv
    - Locally installed font converter that runs on the command line, requires node.js.
 
Here are some resources for downloading free fonts online:
- **Google Fonts**: https://fonts.google.com
- **DaFont**: https://www.dafont.com

### Global Elements

Global Elements are settings and screen elements that apply to the entire UI, regardless of which tile/page you are currently viewing. Click the **+** button in the top row to add a new Global Element to the configuration table.

#### Element Descriptions

The following explains the available element types and their settings:

| Type       | Description | Settings |
|------------|-------------|----------|
| Brightness | Screen brightness control. | <ul><li>*Data Type & Name*: The data source that will provide the current screen brightness. The data should send a number between 0 (completely dark) and 255 (max brightness).</li><li>*Default Brightness*: The screen brightness used when the data source is not being used. Enter a number between 0 (completely dark) and 255 (max brightness).</li></ul> |
| Screen     | Default style settings for the screen. | <ul><li>*Rotation*: Rotates the screen clockwise from the starting position. Select between 0 degrees (landscape), 90 degrees (portrait), 180 degrees (inverted landscape), or 270 (inverted portrait).</li><li>*Scrollbar*: Turns on a scrollbar which is displayed when more than one Tile is configured.</li><li>*Background Color*: This will set the color of the background of the entire screen.</li><li>*Gradient Color*: This color will be used along with the specified *Background Color* to create a vertical gradient.</li></ul> |
| Scroll     | Scroll settings for scrolling between Tiles. | <ul><li>*Data Type & Name*: The data source that will provide the current Tile to be shown. The data should send a number representing the [Tile index number](#tiles).</li></ul> |
| Status     | Ability to show various status items. | <ul><li>*WiFi Status Icon*: Turning this on will display a WiFi symbol in the top right of the screen which will change color depending on the WiFi connection status.</li><li>*WiFi Icon Size*: Sets the size of the WiFi icon if it has been enabled.</li><li>*Debug Text*: Turning this on will display debug information at the bottom of the screen, such as connection status and a WPS (websockets per second) counter.</li></ul> |
| Text       | Default style settings for screen text. | <ul><li>*Default Font, Size & Color*: Sets the default font, font size and color for all text on the screen, unless a more specific style has been selected.</li><li>*Description Font, Size & Color*: Sets the default font, font size and color for description text on the screen, unless a more specific style has been selected. This overrides the *Default Font* settings.</li><li>*Value Font, Size & Color*: Sets the default font, font size and color for value text on the screen, unless a more specific style has been selected. This overrides the *Default Font* settings.</li></ul> |
| Title      | Shows title information for the currently selected Tile. | <ul><li>*Font, Font Size & Text Color*: Sets the font, font size and color for the Title text.</li><li>*Separator*: Turns on a horizintal separator line under the Title area. The color matches the Title's *Text Color*.</li><li>*Background Color*: This will set the color of the background of the Title area. If this is left unset then no background will be shown.</li><li>*Gradient Color*: This color will be used along with the specified *Background Color* to create a vertical gradient on the Title's background.</li></ul> |

### Tiles

Tiles are containers for individual widgets. You can create just a single Tile, or as many as you need for organizing your data, and then enable scrolling so that the Tiles can be rotated between while in use. Click the **+** button to add a Tile to the configuration, and then give it a Name and a Layout.

Each Tile also has an index number, starting at 0 for the first Tile. Any additional Tiles will add to the index, with the second Tile being 1, the third Tile being 2, and so on.

#### Layouts

There are currently two layout options to choose between, Row and Column. When Row is selected, all Widgets will be added into a single row from left to right. If Column is selected, the Widgets will be added in a single column from top to bottom. Regardless of which layout is selected, all Widgets will be sized automatically and given the same amount of space in their row/column. Each Widget has an option called Next, which will cause that Widget to be placed into a new row/column. Any additional Widgets will be added into the same row/column until another Next option is encountered.

#### Widget Descriptions

In addition to the following settings, each Widget also has an optional Description field that will put Description Text below the Widget.

The following explains the available Widgets and their settings:

| Type   | Description | Settings |
|--------|-------------|----------|
| Arc    | Circular widget showing the data value in an indicator arc with the numeric value in the center. | <ul><li>*Min & Max Value*: Sets the minimum and maximum value that the arc will display.</li><li>*Mode*: Normal mode will display the arc value from left to right, Reverse mode will display the arc value from right to left, and Symmetrical mode will put the mid-point in the middle of the arc.</li><li>*Knob*: Adds a solid circle at the current value point.</li><li>*Foreground Color*: Selects the color of the indicator arc and the knob.</li><li>*Font, Size & Text Color*: Sets the font size and color for the value text.</li></ul> |
| Bar    | Bar widget showing the data value in an indicator bar with the numeric value in the center. The bar will be drawn horizontally or vertically depending on the dimensions of the widget. | <ul><li>*Min & Max Value*: Sets the minimum and maximum value that the bar will display.</li><li>*Mode*: Normal mode will draw the bar from left to right, and Symmetrical mode will put the mid-point in the middle of the bar.</li><li>*Foreground Color*: Selects the color of the indicator bar.</li><li>*Gradient Color*: This color will be used along with the specified *Foreground Color* to create a color gradient on the bar's indicator.</li><li>*Font, Size & Text Color*: Sets the font size and color for the value text.</li></ul> |
| Blank  | Puts a blank space in this spot. Useful for adjusting the positioning of other widgets. | None |
| Chart  | Chart with lines connecting datapoints and the numeric value in the center. A datapoint is added on a fixed interval of one second, whether the data is changing or not. | <ul><li>*Min & Max Value*: Sets the minimum and maximum value for the Y-axis of the chart.</li><li>*Data Color*: Sets the color of the lines and datapoints.</li><li>*Font, Size & Text Color*: Sets the font size and color for the value text.</li></ul> |
| Value  | Numeric value shown in the center of the widget. | <ul><li>*Font, Size & Text Color*: Sets the font size and color for the value text.</li></ul> |

### Save button

Upon clicking the Save button, the configuration will first be checked for any issues. If any are found they will be highlighted red and an alert message will apppear on the page. Once you correct the issues, click Save again to upload the configuration to the CANgage where it will be saved to flash storage and then generate the requested UI. When CANgage reboots, it will load this same config and display it on screen even before the device has connected to the CANserver.

### Memory and status text

Between the Save and Import/Export buttons is a status message area that will generally display the current UI memory used percentage. As you create and save your configuration, this percentage will rise. Be careful not to go to close to 100% since the CANgage firmware could lockup if it runs out of memory.

The status area will also display the result of saving, and some error messages will be shown here as well.

### Import/Export

The entire Display page can be imported and exported with the buttons at the top right of the page. Export will generate a .json file which can be kept as a backup, or shared with others. Import will request this same .json file and once loaded will replace all of the current settings with those from the .json file.

## Files page

The Files page allows you to upload files to CANgage, which is divided into sections by file type. Currently the only section is for uploading Fonts. Click the **+** button to upload a new font, or the Delete button to delete a font. At the top of the page is a status indicator that shows the current Data Storage Used.

![Files](https://github.com/IndyJoeA/CANgage/assets/29404417/90bbbedb-b206-439c-9ad4-1da8951a7752)

