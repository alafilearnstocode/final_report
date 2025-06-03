---
sidebar_position: 4
---

# Design Concept and Rationale
## Concept
ClimateCloset is a web app designed to help users select daily outfits tailored to the local weather and their personal wardrobes. Our main intent is that the app empowers its users, particularly international students and students from outside the Midwest at Northwestern to adjust to Evanston’s climate by making confident, weather-appropriate clothing choices using garments they already own. Users  upload photos of their clothes to create a digital closet. ClimateCloset then generates a suitable outfit based on real-time weather data from OpenWeather and personal activity preferences. 

## Design Rationale
The interface was designed with clarity, ease of navigation and modular functionality in mind with each page playing a specific role in the user’s experience whilst maintaining consistency in layout and tone across the application.

1. ### Home Page
The home page serves as the dashboard for daily outfit suggestions. It displays the user’s location, current weather conditions and a generated outfit preview. A “See More” button allows users to: 
- Shuffle the suggested outfit
- Modify criteria such as activity type or duration outdoors
- Provide feedback on comfort and preference
At the bottom of the page, users can find an hourly weather breakdown for deeper context. We intend to prioritise quick access to essential information while enabling deeper interactions with just one click with this layout. (See Figure 1)

<p align="center">
  <img src="/img/design-1.png" alt="Design 1" style={{ width: "450px", maxWidth: "100%", height: "auto", border: "1px solid #ccc" }} />
</p>
<p align="center"><em>*Figure 1: Homepage of the ClimateCloset*</em></p>

2. ### Calendar Page
This page acts as a reflective tool allowing users to:
- Review past outfits and the criteria that informed them
- View their own feedback on outfit comfort
- Observe patterns in how climate and clothing choices interact over time
With this historical dimension, we intend to encourage users to develop a more intuitive sense of what to wear in various weather scenarios. (See Figure 2)

<p align="center">
  <img src="/img/design-2.png" alt="Design 2" style={{ width: "450px", maxWidth: "100%", height: "auto", border: "1px solid #ccc" }} />
</p>
<p align="center"><em>*Figure 2: Calendar page of the ClimateCloset*</em></p>

3. ### Wardrobe Page
The wardrobe organises user-uploaded clothing into six categories:
- weaters & Hoodies
- Tops
- Jackers
- Bottoms
- Footwear
- Accessories

Users can easily upload new items, retag existing ones or remove outdated clothing. With an analytics section in the bottom, each of the items is visually analysed and tagged for seasonality, warmth and formality. This is the core dataset from which outfits are composed. (See Figure 3)

<p align="center">
  <img src="/img/design-3.png" alt="Design 3" style={{ width: "450px", maxWidth: "100%", height: "auto", border: "1px solid #ccc" }} />
</p>
<p align="center"><em>*Figure 3: Wardrobe page of the ClimateCloset*</em></p>

4. ### Settings Page
The settings page offers basic but essential customisation options including an account logout and theme toggling (dark/light mode). Its minimalist design ensures that users can personalise their experience without being overwhelmed by options. (See Figure 4)

<p align="center">
  <img src="/img/design-4.png" alt="Design 4" style={{ width: "450px", maxWidth: "100%", height: "auto", border: "1px solid #ccc" }} />
</p>
<p align="center"><em>*Figure 4: Settings page of the ClimateCloset*</em></p>
