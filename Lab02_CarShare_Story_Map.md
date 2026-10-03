# Lab 02: Car-Share Story Map

**Name:** [Your Full Name]
**Index Number:** [Your Index Number]

## System Description
A digital peer-to-peer car sharing application where **car owners** list their vehicles and **renters** book them for short periods. The app handles listing, searching, booking, payment, pick-up, return and reviews.

## Users
- **Renter:** a person who wants to rent a car for a few hours or days.
- **Car Owner:** a person who lists their car to earn money when they are not using it.

---

## User Story Map

The **backbone** runs left to right (the journey in order). Stories under each step are ordered from most basic (top) to most advanced (bottom).

| Backbone | 1. Sign Up & Verify | 2. List a Car | 3. Search for a Car | 4. Book & Pay | 5. Pick Up & Unlock | 6. Drive & Support | 7. Return & Review |
|---|---|---|---|---|---|---|---|
| **Release 1: Walking Skeleton** | Register with phone number and email | Owner adds car details and one photo | Search by location and date | Pay by mobile money or card | Meet owner and collect the key | Call owner or helpline in an emergency | Return car to owner and confirm return |
| **Release 2: Value Expansion** | Upload driver's licence and ID for verification | Owner sets price and availability calendar | Filter by price, car type and transmission | Instant booking without owner approval | Keyless unlock using phone | In-app chat between owner and renter | Rate and review the owner and the car |
| **Release 3: Competitive Delight** | Biometric (face) identity check | Smart price suggestions based on demand | AI recommendations based on past trips | Split payment between friends | Digital damage inspection with photos | Live GPS tracking and breakdown assistance | Automatic fuel and mileage charges, loyalty rewards |

---

## Release Slices

### Release 1: Walking Skeleton (MVP)
One thin story from every step, so a complete rental works from start to finish:
1. Register with phone number and email
2. Owner adds car details and one photo
3. Renter searches by location and date
4. Renter pays by mobile money or card
5. Renter meets the owner and collects the key
6. Renter can call the owner or a helpline in an emergency
7. Renter returns the car and the owner confirms the return

### Release 2: Value Expansion
Makes the app safer, faster and more convenient: licence verification, availability calendar, filters, instant booking, keyless unlock, in-app chat, and ratings and reviews.

### Release 3: Competitive Delight
Features that set the app apart from competitors: biometric identity checks, smart pricing, AI recommendations, split payments, photo damage inspection, live GPS tracking, and loyalty rewards.

---

## Walking Skeleton MVP: Explanation

The Walking Skeleton is the thinnest end-to-end flow that still lets a real person complete one full car rental. It is not the full product, but every step of the journey exists in a basic form.

- **Sign up:** Without an account, the app cannot know who is renting or owning a car, so a simple phone and email registration is the minimum.
- **List a car:** Without at least one car listed, there is nothing to rent. One photo and basic details are enough at this stage.
- **Search:** Renters must be able to find a car. Searching by location and date is the simplest useful way.
- **Book and pay:** The app needs a way to confirm the booking and move money. Mobile money and card cover the most common payment methods.
- **Pick up:** A physical key handover with the owner is the simplest way to start, since keyless technology can come later.
- **Support:** A phone call to the owner or a helpline covers emergencies without building a full chat system.
- **Return:** The owner confirms the car is back, which closes the rental.

This slice avoids over-building early features (the **vertical siloing** anti-pattern from the lecture) while later steps stay unbuilt. Once the skeleton works, the team can test it with real users and improve it in Releases 2 and 3.
