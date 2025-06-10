---
sidebar_position: 2
---

# Introduction

### Problem
Many college students struggle with dressing appropriately for the volatile Midwest weather. This is a prevalent issue among college students coming from the South, West, or abroad. Problems associated with this unfamiliarity with the weather extend beyond mere discomfort and time waste, and can also have a profound impact on a person’s overall physical health, well-being, and ability to fit in. 

1. Physical Discomfort and Inefficiency
Students who aren’t dressed appropriately for the weather often experience daily discomfort, being too cold, too hot, or wet from unexpected snow/rain. This problem was also identified when many of our users expressed that discomfort can distract them during class, reduce concentration, and make campus life more tiring and unpleasant. The time wasted on choosing the right outfit or constantly needing to return to dorms to change or dry off can also make students late for commitments or less productive overall.

2. Negative Impacts on Physical Health
Failing to dress properly, like wearing light clothes during cold weather or not using waterproof gear in rain, can have serious health consequences. Exposure to cold temperatures without adequate layering can weaken the immune system, increase the risk of respiratory infections, and worsen chronic conditions like asthma [1].

3. Difficulty Fitting in Socially, Especially those from Abroad
For many students, especially from abroad, unfamiliar weather can make it harder to dress like their peers, leading to feelings of isolation. Wearing clothes that aren’t weather-appropriate or culturally typical can make them stand out as “outsiders.” Matching local styles helps them blend in, feel more confident, and connect socially, making it easier to adapt to campus life.

To better understand the landscape of existing solutions and identify opportunities for innovation, we conducted both patent research and a competitive benchmark analysis. As shown in Appendix C, our patent research focused on identifying technologies related to automated outfit generation, weather-responsive clothing recommendations, and wardrobe digitization. Several relevant patents were identified, particularly those integrating real-time weather data with clothing databases or utilizing machine learning to suggest attire. These findings helped us avoid infringement risks while also inspiring the development of personalization features. Meanwhile, Appendix D presents a competitive benchmark of existing products on the market, such as mobile apps and smart wardrobe systems. We evaluated competitors across key dimensions like personalization, weather adaptation, interface usability, and wardrobe management. Our analysis revealed that while some solutions offer personalized suggestions or climate-inspired recommendations, few integrate both effectively with a user-uploaded wardrobe. This gap in the market reinforces the unique value of our system and guides key design choices in our prototype.


### The Project
This project is aimed at helping college students unfamiliar with the weather to dress comfortably and appropriately. The need for a solution is reflected in our end-user interviews. A large majority of our user group has expressed discontent associated with the dressing-up experience (see Appendix A). 

To better understand this problem, we conducted in-person user testing with six students who identified weather-related dressing as a challenge (see Appendix I). We presented four early mockups: Sectioned Wardrobe, Clothes-Share, Style Chatbot, and Style Uploader (see Appendix E). Through these sessions, we gathered direct feedback on usability, desirability, and practicality. Style Chatbot and Style Uploader stood out. Participants especially valued personalized, low-effort, and visually clear solutions that could help them make decisions quickly and confidently.

From these findings, we identified key project requirements ranked from most important to least important (see Appendix B). Our solution had to meet the following criteria:

- Personalize outfits to the user's style and wardrobe 

- Work quickly and reliably daily

- Function across devices and browsers

- Remain affordable for both users and developers


Our final solution, ClimateCloset, directly responds to these needs. ClimateCloset is a mobile and desktop-friendly application that generates daily, weather-appropriate outfits using the user’s clothing. Users upload photos of their wardrobe once, and each day receive a personalized outfit based on real-time weather data in their location. If the user lacks weather-appropriate items, ClimateCloset recommends a substitute from its internal closet and flags the gap. Users can customize their outfits, add new items anytime, and rate the previous day's outfit for warmth and style, allowing the system to improve and customize to the user’s temperature tolerance over time (see Appendix F).

By reducing uncertainty, saving time, and increasing comfort and confidence, ClimateCloset helps students adapt to a new climate and social environment with ease. The remainder of this report outlines the step-by-step development of our solution to the problem at hand. It details our target users, design requirements, and the reasoning behind key decisions made throughout the process. Additionally, it includes guidance for future iterations and discusses the limitations of our current design.
