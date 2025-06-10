---
sidebar_position: 2
---
# Appendix B: Project Definition
**Project Name:** ClimateCloset <br />
**Team Members:** Alafi Dumo, Kevin Wu, Esabella Fung, Joshua Lee <br />
**Date:** 5/19/2025 <br />
**Version:** 3 <br />

**Mission Statement:**
Create an easy-to-use, personalized service that helps college students from outside the Midwest to dress more appropriately for potentially unfamiliar weather conditions. 

## Project Deliverables:
- Final Report: Website (Due June 9, 2025)
- Infomercial (Due June 9, 2025)
- Working App (Due June 9, 2025)
- Slide Presentation (Due June 9, 2025)

## Design Constraints:
- Legal: The limitations to how we collect data and how much data we can collect.
    - Privacy constraints on people opting out of cookies (in Europe, people are always given a popup with the option to opt out of cookies)
    - Privacy constraints on the Collection of PII (location services, name, email address (for login))
        - Users should give their consent to the collection and use of their data (HIPAA, GDPR, CCPA)
        - Data encryption prevents PII from being stolen
    - Ethical constraints on how the data is being used
        - Data should not be sold to other companies.
        - Transparent disclosure on what we are using collected data for. 
- Time: We have one quarter to work on this project
- Budget: We were given 150 Dollars in total to work on this project

## Users and Stakeholders
- Potential users include any individuals who have to travel/live in locations with weather drastically different from their home.
- Our specific target audience is students who go to college in the Midwest with drastically different weather conditions than they are used to.

## User Profile
*An international student from Thailand is attending Notre Dame University in the state of Indiana. They do realize the drastic difference in the climate between the Midwest and Thailand, so they bought puffer jackets and other thick clothes. However, they do not know how to layer up their clothes and have no concept of what temperatures below freezing point feel like. This makes it particularly difficult for them to choose their outfit, especially during the brutal winter.*

## Illustrative User Scenario
Tom H., a 19-year-old man, is a Northwestern international student from Tijuana, Mexico. Tom finds it difficult to adapt his clothing style to the weather of the Midwest. He does not want to buy excessive amounts of clothes beyond what he already owns. Tom has asked his friends about what they wear, but because his friends are more acclimated to the cold, they tend to wear less layers than he is comfortable with in the winter. While he is exploring style inspirations and guides on Pinterest, Tom still struggles to choose the right outfit for the volatile weather of the Midwest. In the morning, Tom finds that he is spending excessive amounts of time trying to guess which clothes he should wear for the day. Although he has already learnt the lesson to check ‘feels like temperature’ instead of the ‘actual temperature,’ he still doesn’t know what to dress for weather conditions such as rain and snow. Tom finds this experience especially frustrating when he ends up underdressing, and has to rush to class with freezing or wet weather he is not prepared for. He craves a solution that will help him with choosing the right outfit. 

## Project Requirements

| Category         | Needs (from most important to least important)                        | Metrics                | Units                                                     | Ideal Value                | Allowable Value                  |
|------------------|------------------------------------------------------------------------|-------------------------|------------------------------------------------------------|-----------------------------|----------------------------------|
| Personalization  | Create an outfit in a style the user wants                            | User testing            | Out of 10                                                  | 10                          | 6.5                              |
|                  | Outfit generation using user’s uploaded wardrobe                      | Objective               | Yes/No                                                     | Yes                         | Yes                              |
|                  | Takes user’s feedback on previous suggestions into account            | User testing            | Out of 10                                                  | 10                          | 6.5                              |
| Functionality    | Reliable to use on a daily basis                                      | Objective Testing       | Testing percentage                                         | Accurate 95% of the time during testing | Accurate 80% of the time during testing |
|                  | Fast at responding to outfit generation requests                      | Request to Response Time| Seconds                                                    | 0.1                         | 8.25 (Avg Adult attention span) |
|                  | Quick to Use                                                           | Testing                 | Minutes it takes to upload photos on first try (20 clothes)| 8 minutes                  | 10 minutes                       |
| User Experience  | UI is easy to navigate                                                | User Rating             | Out of 10                                                  | 10                          | 6.5                              |
| Accessibility    | Works on multiple screen sizes                                        | Objective               | Pixel sizes                                                | 640x480, 800x600, 1024x768  | 640x480, 800x600, 1024x768      |
|                  | Works on multiple browsers                                            | Objective               | Yes/No                                                     | Chrome, Safari              | Chrome, Safari                  |
|                  | Font size is large enough to read easily                              | Objective               | Font Size                                                  | 18 px                       | 12 px                            |
|                  | Large Buttons                                                         | Objective               | Points                                                     | 44×44 points                | 40×40 points                    |
| Cost             | Affordable for User                                                   | Cost                    | $                                                          | $0                          | $2 per month                    |