---
sidebar_position: 8
---

# Appendix H: Design Review Summary

We held our design review on Friday, May 16, 2025 with our classmates and professors. We presented our two mockups which included a Figma design of how our solution would look on a web browser and a walkthrough of our AI outfit suggestion pipeline. We asked our classmates and professors for feedback on our solution’s layout, features, and descriptions.

First, we demonstrated how our planned interface would function using a Figma mockup, showing the clothing swipe feature of our app, and listing possible directions we were considering in terms of our design. We received feedback about additional weather-related information we should include as well as feedback regarding the aspect ratio that we should design our mockup with. Next, we presented the AI-outfit suggestion pipeline, which included a flow chart representing our pipeline’s logical processes, as well as our own evaluation of its performance. For this mockup, we received feedback mostly about what other AI-powered features we could include to improve personalization. In general, our feedback could be categorized into 3 categories: UI-related changes, AI-powered personalization features, and additional comments regarding the idea at large. Overall, we were pointed in the direction of increasing both the level of personalization and the level of information displayed–something that contradicts our original assumption that the simpler our system was, the better it would be for users. 

## Summary of Design Review Results

<p align="center"><em>Table 1: Summary of Design Review Results</em></p>


| **Design Layout** | **Functionality** | **Additional Features** |
|------------------|------------------|-------------------------|
| <u>**Weather Information**</u> <br /> Weather should include “feels like” temperature and wind speed. <br /> Notes on the weather should be included with the weather widget. <br /> The weather widget should remain the same size with a drop down menu that includes additional information about the weather. | <u>**Layers**</u> <br /> Users should be able to layer two types of outerwear (ex: sweater and jacket). <br /><br /> <u>**Integrated Feedback**</u> <br /> The solution should include a machine learning algorithm which takes into account user feedback for how good the generated outfit was for future output. | <u>**Daily Plans**</u> <br /> Users should be able to input what they are doing for the day which influences the outfit the algorithm chooses (ex: if the user is attending a conference, they should wear a suit). <br /><br /> <u>**Clothing Log**</u> <br /> The solution should track which clothes were recently worn so users are not recommended to wear the same clothes several days in a row. |
| <u>**Mobile UI**</u> <br /> People are unlikely to access our solution through a web browser. We should design an interface which is easily accessed through a mobile application. <br /> Have the user include their own preferred widgets and rearrange page since it's on laptop. <br /> Hourly information on weather should be displayed. | <br /> Feedback on style should influence the outputted outfit. <br /><br /> Include another page with a calendar that saves the outfits you wore and you include a note about how when the outfit worked for weather (shows past outfits and if it fit the weather). <br /><br /> <u>**User Login**</u> <br /> Users should have the option to make an account to store their clothes in a virtual closet. | <u>**Missing Clothes**</u> <br /> If the user does not have appropriate clothes, they should be recommended proper clothes. <br /><br /> <u>**Additional Personalization**</u> <br /> Clothes color matching tool that helps coordinate colors. <br /> Add a slider for prioritization of fashion vs. comfort. |


## Action Plan
<p align="center"><em>Table 2: Action Plan</em></p>

| **Suggestion/Criticism** | **Plan to Address Feedback** |
|--------------------------|------------------------------|
| <u>Mobile UI</u> <br /> 1. Our team should design an interface which is easily accessed through a mobile application. | 1. Using Figma to create a mobile UI and testing different layout formats. Then, use an app development tool to create a prototype for mobile. |
| <u>>Weather Information</u> <br /> 1. Weather should include “feels like” temperature and wind speed. <br /> 2. Notes on the weather should be included with the weather widget. <br /> 3. The weather widget should remain the same size with a drop down menu that includes additional information about the weather. | 1. Adding “feels like” temperature and wind speed to the weather widget. <br /> 2. Moving weather notes from the clothing description section to the weather widget. <br /> 3. Designing a drop down on the weather widget and testing how to import additional weather data for weather conditions throughout the day. |
| <u>Layers</u> <br /> 1. Users should be able to layer two types of outerwear (ex: sweater and jacket). | 1. Adding multiple outerwear sections so users can layer two or more types of outerwear. |
| <u>Fill in Missing Clothes</u> <br /> 1. The system should inform users the clothes they are lacking to dress up adequately for the weather. | 1. An advice section on the app that analyzes the weather and the available clothes to advise clothing purchase. <br /> 2. A general evaluation metric that tells the user how equipped their wardrobe is for the city, perhaps with a circular progress bar. |
| <u>User Login</u> <br /> 1. Users should have the option to make an account to store their clothes in a virtual closet. | 1. Create a backend database to store user information. |
| <u>Additional Personalization (Daily Plan)</u> <br /> 1. Users should be able to input what they are doing for the day which influences the outfit the algorithm chooses (ex: if the user is attending a conference, they should wear a suit). | 1. Add the user’s daily activities as an optional part of the prompt for outfit generation. |
