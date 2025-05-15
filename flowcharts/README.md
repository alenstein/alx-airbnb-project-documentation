## Flowchart: Property Booking Process

This section details the flowchart created to visualize the step-by-step workflow for a key backend process: **Property Booking**. This diagram helps in understanding the sequence of operations, decision points, and data interactions from the moment a guest initiates a booking until its completion.

---
### Flowchart Overview

The flowchart illustrates the sequence of actions and decisions in the property booking process.

**Key Elements in the Flowchart:**

* **Process Steps (Rectangles):** Represent actions or operations performed by the system or user (e.g., "Calculate Total Price," "Create Booking Record in DB").
* **Decision Points (Diamonds):** Indicate points in the process where a decision is made, leading to different paths (e.g., "Check Property Availability in DB," "Guest Confirms to Book & Proceed to Payment").
* **Flow Lines (Arrows):** Show the direction of the process flow from one step to another. Lines coming from decision points are often labeled with the condition (e.g., "Property Available," "Payment Successful").
* **Start/End Points:** Indicate the beginning and end of the process.

---

### Breakdown of the Property Booking Process (as per the flowchart):

1.  **Booking Initiation:**
    * A `Guest` selects a property, desired dates, and number of guests, then initiates a booking request.

2.  **System Reception & Availability Check:**
    * The system receives the request.
    * **Decision:** It checks the property's availability in the database for the requested dates.
        * If **Not Available**: The guest is informed, and the process ends.
        * If **Available**: The process continues.

3.  **Pricing & Guest Confirmation:**
    * The system calculates the total price for the booking.
    * The booking summary and price are displayed to the guest.
    * **Decision:** The guest confirms whether to proceed with the payment or cancel.
        * If **Cancelled**: The process ends.
        * If **Confirmed**: The payment process is initiated.

4.  **Payment Processing:**
    * The system initiates the payment.
    * **Decision:** The payment is processed via an external Payment Gateway.
        * If **Payment Failed**: The guest is informed and may be given an option to retry or cancel (which could loop back to the guest confirmation step).
        * If **Payment Successful**: The process continues.

5.  **Booking Finalization & Notifications:**
    * A booking record is created in the database with a "Confirmed" status.
    * The property's availability is updated in the database (marking the dates as booked).
    * A booking confirmation is sent to the `Guest` (via email and/or in-app notification).
    * A new booking notification is sent to the `Host` (via email and/or in-app notification).

6.  **Process End:** The booking process is successfully completed.

This flowchart provides a clear visual guide to the interactions and logic involved in the property booking feature of the Airbnb Clone backend.

---

**File Location:**

* The flowchart diagram image is stored as `data-flow-diagram.png` in the `flowcharts/`
