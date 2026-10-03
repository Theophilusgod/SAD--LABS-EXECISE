# Lab 01: FURPS+ Drone Delivery

**Name:** [Your Full Name]
**Index Number:** [Your Index Number]

## System Description
An autonomous drone that delivers drugs from a central pharmacy or depot to a hospital. It plans its own route, flies without a pilot, lands at a designated point, and hands over the package to authorized hospital staff.

---

## F: Functionality
*(Functional requirements)*

1. The drone shall accept a delivery order from the hospital system and generate a flight route automatically within 60 seconds of receiving the order.
2. The drone shall land within 0.5 m of the designated landing marker in wind speeds of up to 25 km/h.
3. The drone shall detect obstacles at least 15 m ahead and automatically reroute or hover without colliding.
4. The drone shall unlock its cargo bay only for authorized hospital staff verified by PIN or staff badge.
5. The drone shall send a delivery confirmation to the hospital system within 10 seconds of the package being collected.

## U: Usability
*(Non-functional)*

1. A nurse with no more than 15 minutes of training shall be able to receive a package without assistance.
2. The drone shall give status alerts (arrival, unlocking, error) in both audio and on-screen text.
3. Any user error, such as a wrong PIN, shall display a clear message and allow up to 3 retries before locking the cargo bay and notifying the hospital security desk.
4. The staff interface shall meet WCAG 2.1 Level AA accessibility guidelines.

## R: Reliability
*(Non-functional)*

1. The drone service shall achieve a mission success rate of at least 99.5% of dispatched deliveries.
2. If the drone loses communication for more than 10 seconds, it shall automatically return to base or land at the nearest safe landing point.
3. If battery level falls below 20% during a mission, the drone shall abort and return to the nearest charging station.
4. The drone shall have a mean time between failures (MTBF) of at least 500 flight hours.

## P: Performance
*(Non-functional)*

1. The drone shall complete a delivery within 30 minutes of dispatch for distances up to 20 km.
2. The drone shall carry a payload of up to 2 kg.
3. The drone battery shall support a round trip of at least 40 km on a single charge.
4. The system shall handle at least 50 simultaneous delivery orders across the drone fleet without delaying route planning beyond 60 seconds.

## S+: Supportability and Constraints
*(Non-functional, plus design and regulatory constraints)*

1. The drone software shall support over-the-air updates without requiring a physical visit to the drone.
2. The drone shall log every flight, including route, time and errors, and keep these logs for at least 12 months for audits.
3. The drone shall comply with the national civil aviation authority's regulations for unmanned aerial vehicles.
4. The cargo bay shall maintain a temperature between 2°C and 8°C for temperature-sensitive medicines such as vaccines.
5. The drone shall not operate in rain heavier than 5 mm/hour or in wind speeds above 35 km/h.
6. The drone shall integrate with the hospital's inventory system through a secure, encrypted API.
