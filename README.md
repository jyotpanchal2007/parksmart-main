# ParkSmart

Smart Parking Management System demo with separate administrator and user dashboards.

## Run locally

From this project folder, start a static web server:

```powershell
python -m http.server 5500
```

Open [http://localhost:5500](http://localhost:5500) in a browser.

## Booking behavior

- Users can tap a compatible available slot or let the system match one automatically. The demo map uses P01 Covered, P02 EV Charging (Car/SUV), P03 Near Exit, P04 Covered, and P05 Standard.
- Default hourly rates are Bike ₹40, Car ₹60, and SUV ₹80. Administrators can change them in Settings.
- Save a default vehicle under My Vehicle to prefill bookings. Registration numbers are formatted and validated in the Indian format (for example, `MH 12 AB 1234`).
- Bookings can include an optional arrival time and estimated duration. The reservation expires 15 minutes after the arrival time (or 15 minutes after booking for an immediate arrival); users can check in, extend the window, or cancel before check-in.
- A digital entry pass displays the booking ID. After check-in, a live timer and running fee estimate appear. End Parking & Pay records the completed visit and simulated payment, with printable and downloadable receipts in My Payments.
- Admin View controls the administrator-only global search and overview statistics. User accounts only see their own parking data.

## Data structures and complexity

- **Fixed slot array:** P01–P05 map directly to indexes for O(1) bay lookup and slot-count updates. Searching for a compatible bay scans at most five slots, so it is O(5) (O(n) for a generalized lot).
- **Circular FIFO queue:** Waiting requests are enqueued/dequeued in O(1) time; peeking is O(1), while finding a request's position takes O(n).
- **Doubly linked lists:** Parking history and payment receipts are inserted at the head in O(1) and traversed in O(n). Saved vehicles and each booking's time-extension chain also use linked lists.
- **AVL search tree:** Vehicle registrations are indexed by normalized plate for O(log n) worst-case insert and exact lookup. Admin gate lookup and global vehicle search use this tree.
- **Decision tree:** Vehicle type, EV requirement, and bay preference determine the search order; the fixed lot scan remains bounded by five bays.

The fee is calculated from actual parking time, rounded up to a whole hour with a one-hour minimum. The duration and +30-minute extensions are shown in the booking record and receipt.

## Demo limitations

The application is a browser-only demo. Its state is saved in `localStorage`, so it is not a shared database and cannot guarantee atomic reservations across separate tabs, browsers, or devices. Authentication, gate check-in, and payments are simulated; production use requires a trusted server, real authentication, and server-side transactional slot allocation.

## Demo access

- Administrator: username `admin`, password `admin`
- User: create a demo account by choosing a username and password on first sign-in

To demonstrate the data structures, try booking all five bays, submitting another booking to join the FIFO queue, and checking out or releasing a bay to see the next compatible request assigned. Try an EV request (P02), a covered/near-exit preference, an admin plate search, and a user viewing their own history and receipt.
