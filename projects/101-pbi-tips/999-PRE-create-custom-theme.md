---
title: Create Custom a Theme
layout: page
---

When creating a visual, Power BI Desktop uses a default color palette and font. You can customize your visuals by manually changing their color and font.   

Alternatively, you can import a [Power BI Desktop theme](https://docs.microsoft.com/en-us/power-bi/create-reports/desktop-report-themes), indicating what color pallet and font to use. You have the option to choose one of the many built-in themes or to customize your own.   

Say you have created a report using the default pallet and font:  

![](/asset/screenshot/999-PRE-create-custom-theme-img01.png)

Now, you would like to customize it by making the report color-blind friendly and use the font Courier New is all visuals:  


1. Create a report theme by building a JSON file according to your preferences. In our case, the JSON file is readily available [here](https://community.powerbi.com/t5/Themes-Gallery/Color-Blind-Friendly/m-p/140597). We just need to adjust it to include the font type Courier New.  

		{ 
			"name": "ColorblindSafe-Longer", 
			"dataColors": ["#074650", "#009292", "#fe6db6", "#feb5da", "#480091", "#b66dff", "#b5dafe", "#6db6ff", "#914800", "#23fd23"],
			"background":"#FFFFFF",
			"foreground": "#074650",
			"tableAccent": "#fe6db6",
			"visualStyles":{
				"*": {
					"*": {
						"*":  [{
								"fontFamily": "Courier New"
							}]
					}
				}
			}
		}

2. Save text above as a JSON file.  

3. From Report Editor, go to **View** > **Themes** > **Browse for themes**.  

	![](/asset/screenshot/999-PRE-create-custom-theme-img03.png)

3. Find the JSON file and open it. The theme is then imported and your visuals update with the new theme specifications.  

	![](/asset/screenshot/999-PRE-create-custom-theme-img02.png)

Keep a template .pbix file where the theme JSON file is already uploaded. In this way, you and your colleagues can create new reports with the organization’s theme right away. 