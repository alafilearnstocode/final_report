---
sidebar_position: 4
---

# Design Concept and Rationale
## Concept
ClimateCloset is a web app designed to help users select daily outfits tailored to the local weather and their personal wardrobes. The main intent for this app is to empower its users, particularly international students and students from outside the Midwest at Northwestern, to adjust to Evanston’s climate by making confident, weather-appropriate clothing choices using garments they already own. Users upload photos of clothes from their wardrobes to create a digital closet. ClimateCloset then generates a suitable outfit based on real-time weather data from OpenWeather and personal activity preferences. After creating an initial design on Figma of ClimateCloset, our team received feedback and improved upon our early iteration for the current prototype of ClimateCloset (see Appendix H). 
Instructions to replicate ClimateCloset are listed in Appendix G, and instructions for general users are in Appendix J. Further developer-oriented documentation covering stack architecture and the ClimateCloset API (Application Programming Interface) is provided in Appendix L.


## Design Rationale
The interface was designed with clarity, ease of navigation, and modular functionality in mind, with each page playing a specific role in the user’s experience whilst maintaining consistency in layout and tone across the application.

1. ### Home Page
The home page serves as the dashboard for daily outfit suggestions. It displays the user’s location, important general weather information, and an option to create an outfit for the day (Figure 1). 

<p align="center">
  <img src="/img/design-1.png" alt="Design 1" style={{ width: "450px", maxWidth: "100%", height: "auto", border: "1px solid #ccc" }} />
</p>
<p align="center"><em>*Figure 1: Homepage of the ClimateCloset*</em></p>

**Weather Information** <br />
The weather information includes current temperature, general weather conditions (i.e. sunny, windy, cloudy, rainy), the “feels like” temperature, high-low temperature, the chance of rain, and wind speed since users indicated these pieces of weather information played a critical role in how they dressed for the day. Below the general weather information, users have the option to create an outfit for the day.

**Outfit Generation** <br />
Below the general weather information, users have the option to create an outfit for the day. When the user selects “Create Outfit for Today,” they are asked a questionnaire based on how active they will be, what time range they will be out, and what occasion they will be dressing for. This questionnaire is used to better inform the ClimateCloset algorithm how to dress the user and improve ClimateCloset’s output. Below the questionnaire, there is an extra notes section that is helpful if users want to wear a particular outfit for the day or if there are any specific preferences they have for their outfits. Once the outfit is created, users can see their generated outfit, as well as a “weather match” and “fashion match” score (notably, this is generated as part of the LLM output, and is not based on a “hard algorithm,” meaning the scores are subjective). The interface also displays AI reasoning as to why ClimateCloset generated the outfit, and the option to reshuffle or delete the outfit (Figure 2). Furthermore, users can edit their responses to the questionnaire for a newly generated outfit. They may also improve ClimateCloset’s output by giving an overall rating, temperature rating, or textual feedback on the outfit for the day.
After the outfit is generated, users can see their generated outfit on the home screen below the general weather information category with a “See more” button, which brings the user back to their generated outfit window (Figure 2).

**Additional Weather Information** <br />
Below the “Create Outfit” menu, there are an additional two boxes. The first box contains information about the hourly weather for the next 24 hours, and the second box contains information about the daily high/low weather for the next 7 days. This section was added to provide the user with a more thorough understanding of the upcoming weather, allowing them to assess the generated outfits, and modify them as needed.

<p align="center">
  <img src="/img/gen_outfit.png" alt="Gen Outfit" style={{ width: "450px", maxWidth: "100%", height: "auto", border: "1px solid #ccc" }} />
</p>
<p align="center"><em>*Figure 2: Generated Outfit Page*</em></p>


2. ### Calendar Page
On the calendar page, users can review past outfits and what criteria informed their outfits. Clicking on a day that has an outfit will bring up the “go to outfit” button while clicking on a day that does not have an outfit will bring up the “create outfit for this day” button.
By storing all outfits that the user has created in the past, as well as the reasoning and feedback for that outfit, we will inform our users about patterns in how climate has informed their clothing choices over time. In doing so, we intend to encourage users to develop a more intuitive sense of what to wear in various weather scenarios (Figure 2).


<p align="center">
  <img src="/img/calendar.png" alt="calendar" style={{ width: "450px", maxWidth: "100%", height: "auto", border: "1px solid #ccc" }} />
</p>
<p align="center"><em>*Figure 3: Calendar page of the ClimateCloset*</em></p>

3. ### Wardrobe Page
The wardrobe organises user-uploaded clothing into six categories:
- Sweaters & Hoodies
- Tops
- Jackers
- Bottoms
- Footwear
- Accessories

Categorization was added to add a sense of organization to the digital wardrobe and to prevent clutter over time, as the wardrobe size increases (Figure 4). By clicking into each section, users can easily upload new items, re-tag existing ones, or remove outdated clothing (Figure 5). 
Through an analytics section below the categories, users can get a sense of how well-adapted their wardrobe is to their city (this currently has been set to only work for Evanston) and what clothing they need to purchase.


<p align="center">
  <img src="/img/wardrobe.png" alt="Wardrobe" style={{ width: "450px", maxWidth: "100%", height: "auto", border: "1px solid #ccc" }} />
</p>
<p align="center"><em>*Figure 4: Wardrobe page of the ClimateCloset*</em></p>
<p align="center">
  <img src="/img/w_section.png" alt="Wardrobe Section" style={{ width: "450px", maxWidth: "100%", height: "auto", border: "1px solid #ccc" }} />
</p>
<p align="center"><em>*Figure 5: Wardrobe Section of the ClimateCloset*</em></p>

### Editing Items

From the wardrobe page, users can “edit” each individual wardrobe item. This includes the option to change the name, description, category, and metrics (cold, warm, rain, and wind suitability level) for each item (Figure 6). By letting users edit the items manually, we ensure that users do not have to rely on the AI-generated item description, and if they desire, can add specific notes about each item as well (for example, if they want to wear a certain shirt when going out for a run, they can specify that in the item description). Here, we also give the user the ability to delete items they no longer want to include in their wardrobe. 

<p align="center">
  <img src="/img/picture1.png" alt="Wardrobe Pic" style={{ width: "450px", maxWidth: "100%", height: "auto", border: "1px solid #ccc" }} />
</p>
<p align="center"><em>*Figure 6: Editing Items Page*</em></p>

4. ### Settings Page
The settings page includes general account information and general instructions the user can give to ClimateCloset (Figure 7). On this page, users can see their account name, and log out of their account. Below the account information, users can provide general instructions to ClimateCloset if they have specific requests they want ClimateCloset to apply to all generated outfits. This general instructions section provides additional customization for users. An example input for general instructions would be “I get cold easily and would like all my outfits to include a jacket.”

<p align="center">
  <img src="/img/design-4.png" alt="Design 4" style={{ width: "450px", maxWidth: "100%", height: "auto", border: "1px solid #ccc" }} />
</p>
<p align="center"><em>*Figure 7: Settings page of the ClimateCloset*</em></p>
