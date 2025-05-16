## Use Case Diagram for Airbnb Clone Backend

This section provides an overview of the Use Case Diagram for the Airbnb Clone backend system. This diagram is designed to visually represent the interactions between different users (actors) and the main functionalities (use cases) of the system. It helps in understanding *who* can do *what* with the system.

---
### Diagram Overview

The diagram visually represents the following components:

* **Actors (Rectangles with "Actor:" prefix):** These represent users or external systems that interact with the Airbnb Clone backend.
    * `Actor: Guest`: Represents users looking for, booking, and reviewing properties.
    * `Actor: Host`: Represents users listing, managing properties, and handling bookings for their properties.
    * `Actor: Admin`: Represents administrators who oversee and manage the platform.
    * `External System: Payment Gateway`: Represents the third-party service used for processing payments.
    * `External System: Email Service`: Represents the third-party service used for sending email notifications.

* **System Boundary (Large Rectangle labeled "Airbnb Clone Backend System"):** This encloses all the internal functionalities (use cases) of the backend system.

* **Use Cases (Ovals):** These represent the specific actions or functionalities that the system provides. They are grouped into logical categories within the system boundary for clarity:
    * **User & Profile:** Includes `Register Account`, `Login to Account`, `Manage Own Profile`.
    * **Property & Search:** Includes `Search & Filter Listings`, `View Listing Details`, `Create Property Listing`, `Update Own Listing`, `Delete Own Listing`.
    * **Booking, Payment & Reviews:** Includes `Book Property`, `View Own Bookings (as Guest)`, `View Bookings for Own Properties`, `Cancel Own Booking (as Guest)`, `Process Payment`, `Send Notification`.
    * **Administration:** Includes `Admin: Manage Users`, `Admin: Manage Listings`, `Admin: Manage Bookings`.

* **Relationships (Lines/Arrows):**
    * Lines connecting actors to use cases indicate that the actor participates in that use case.
    * Arrows between use cases suggest a dependency or invocation (e.g., booking a property might lead to processing a payment and sending a notification).
    * Lines connecting use cases to external system actors show direct interaction (e.g., "Process Payment" interacts with the "Payment Gateway").

---

### How to Interpret the Visual Diagram

1.  **Identify Actors:** Look for the rectangular shapes labeled "Actor:" or "External System:" to understand the different users and external services interacting with the system.
2.  **Locate Use Cases:** The ovals within the "Airbnb Clone Backend System" rectangle represent the system's functionalities. They are grouped into categories like "User & Profile" for easier understanding.
3.  **Trace Interactions:** Follow the lines (associations) from an actor to an oval (use case) to see what actions that actor can perform. For example, the `Actor: Guest` is connected to `Book Property`.
4.  **Understand Dependencies:** Arrows between ovals (use cases) or from ovals to external system actors indicate a flow or dependency. For instance, `Book Property` has arrows pointing to `Process Payment` and `Send Notification`, indicating these are subsequent or included actions.

This diagram provides a clear overview of what the system does from the perspective of its users and its interactions with external services.

---

**File Location:**

* The diagram image (e.g., `use-case-diagram.png`) should be stored in the `use-case-diagram/` directory.

