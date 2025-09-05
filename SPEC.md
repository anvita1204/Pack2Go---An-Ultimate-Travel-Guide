# Pack2Go - Project Specification

## Overview

Pack2Go is a smart travel guide web application (MERN stack) that helps users plan, organize, and share trips. It focuses on personalized itineraries, contextual packing checklists, cost estimation, destination discovery, and offline-friendly access to trip details. The platform also supports specialized trip types (holidays, business trips, events), local recommendations (restaurants, hotels, attractions), and premium features like AI-generated itineraries for destinations.

---

## Vision

Enable travellers to go from idea to ready-to-pack in minutes. Pack2Go should make trip planning faster, safer, and more personalized by combining maps, weather-aware packing advice, budget planning, curated recommendations, and smart premium itinerary generation.

---

## Audience

* Students and solo travellers building short trips
* Families and groups planning multi-day itineraries
* Professionals traveling for business
* Event-goers who need a structured travel plan

---

## Must-have Features (MVP)

1. Authentication (email/password, JWT) and user profile (preferences: budget range, travel style, food restrictions)
2. Trip categories: Holidays, Business Trips, Events
3. Itinerary builder (create/edit/delete trips with day-by-day activities)
4. Destination search using Google Maps / Places API (autocomplete + lat/lng)
5. Weather integration (OpenWeatherMap or similar) for trip dates and location
6. Packing checklist generator that adapts to weather, duration, and trip type
7. Budget calculator (transport, accommodation, food, activities, total) and per-day estimate
8. Places to visit, restaurants nearby, hotels to stay (using Places API)
9. Save and load itineraries from MongoDB (per user)
10. Responsive UI (desktop + mobile)
11. Notifications/reminders for upcoming trips or activities
12. Deployable frontend and backend with environment config

---

## Should-have Features (priority after MVP)

1. Activity-level map preview with markers (react-google-maps)
2. Export itinerary as printable PDF and JSON
3. Browse curated popular destinations and events
4. Shareable read-only link for an itinerary
5. Local storage caching for offline editing (single itinerary) with sync on reconnect
6. Simple admin view to manage curated destinations and events

---

## Premium Features (Nice-to-have / Stretch Goals)

1. AI-powered itinerary generator: e.g., for a 5-day stay in Dubai, automatically generate a complete itinerary (places, restaurants, hotels, daily schedule)
2. Advanced destination suggestions based on preferences and budget
3. Currency converter and local cost suggestions (via Exchange Rate API)
4. Public events integration (local events API)
5. Push notifications / email reminders for trip milestones
6. Multi-language support (i18n)
7. Mobile app wrapper using React Native (later)

---

## User Stories

* As a user I can sign up and log in so my itineraries are private and safe.
* As a user I can choose trip type (holiday, business, event) and build my plan accordingly.
* As a user I can create a trip with start/end dates and add activities per day.
* As a user I can search for places, hotels, or restaurants and add them to my itinerary.
* As a user I can see weather forecasts for trip dates and get packing suggestions.
* As a user I can estimate total trip costs and view per-day breakdown.
* As a user I can receive notifications for trip reminders or activity alerts.
* As a premium user I can generate a complete itinerary automatically for a given destination and duration.
* As a user I can export my itinerary to PDF to share with others.
* As a user I can store a cached copy locally and continue editing offline.

---

## Technical Stack

* Frontend: React.js (create-react-app or Vite) with react-router-dom
* Styling: Tailwind CSS (or Material UI)
* Maps & Places: Google Maps JavaScript API + Places API
* Backend: Node.js + Express
* Database: MongoDB (Atlas or local)
* Authentication: bcryptjs for hashing, jsonwebtoken (JWT)
* Weather: OpenWeatherMap API (or WeatherAPI)
* Notifications: Web push (service workers) or email via Nodemailer/SendGrid
* Deployment: Vercel (frontend) and Heroku/Render/AWS (backend)
* Premium AI: OpenAI API
* Version control: GitHub

---

## Data Models (Mongoose schemata - high level)

### User

* \_id: ObjectId
* name: String
* email: String (unique)
* password: String (hashed)
* preferences: { budgetRange: { min, max }, travelStyle: String, foodRestrictions: \[String] }
* isPremium: Boolean (default false)
* createdAt, updatedAt

### Itinerary

* \_id: ObjectId
* owner: ObjectId (User)
* title: String
* tripType: enum('holiday', 'business', 'event')
* description: String
* startDate: Date
* endDate: Date
* days: \[{ date: Date, activities: \[{ time: String, placeName: String, lat: Number, lng: Number, notes: String, category: enum('place','hotel','restaurant'), placeId: String }] }]
* budget: { transport: Number, stay: Number, food: Number, activities: Number, total: Number }
* packingChecklist: \[String]
* visibility: enum('private', 'public')
* notifications: \[{ message: String, date: Date, sent: Boolean }]
* createdAt, updatedAt

### Destination (optional curated list)

* name, country, lat, lng, description, tags

---

## API Endpoints (REST)

Auth

* POST /api/auth/register { name, email, password } -> 201 / error
* POST /api/auth/login { email, password } -> { token }
* GET /api/auth/me -> { user } (protected)

Itineraries

* POST /api/itineraries -> create itinerary (protected)
* GET /api/itineraries -> list user itineraries (protected)
* GET /api/itineraries/\:id -> retrieve itinerary (protected or public-read if shared)
* PUT /api/itineraries/\:id -> update itinerary (protected)
* DELETE /api/itineraries/\:id -> delete itinerary (protected)

Places / Maps

* GET /api/places/autocomplete?q=... -> proxy to Google Places Autocomplete
* GET /api/places/details?placeId=... -> proxy to Google Places Details

Utilities

* GET /api/weather?lat=...\&lng=...\&date=... -> fetch weather forecast
* POST /api/itineraries/\:id/export -> generate PDF
* POST /api/itineraries/\:id/notify -> schedule notifications

Premium

* POST /api/premium/generate-itinerary { destination, days } -> AI-based full itinerary (premium only)

Admin (optional)

* GET /api/destinations -> list curated destinations
* POST /api/destinations -> create curated destination (admin only)

---

## Frontend Pages / Components

* Landing / Home
* Register / Login
* Dashboard (list of user trips + create new)
* Itinerary Editor (meta -> days -> activities -> packing/budget)
* Destination/Places Browser (places, restaurants, hotels)
* Itinerary Viewer (read-only view + share/export)
* Notifications center
* Premium upgrade page
* Settings / Profile (preferences)
* Admin pages (if implemented)

---

## UX Wireframes (textual)

1. Dashboard: lists trips with thumbnail, type, dates, and actions (edit, view, export)
2. Create Trip: title, trip type, dates, travel style, budget range -> next
3. Day Editor: left column calendar/dates, right column activities for that date (time, search place, notes)
4. Packing & Budget: generated checklist (editable) and budget input with auto total
5. View Trip: map with markers for activities, daily schedule, weather badges, nearby hotels and restaurants
6. Premium Itinerary Generator: enter destination and days -> system generates full itinerary

---

## Deployment & DevOps

* GitHub Actions for CI
* Frontend deploy to Vercel / Netlify
* Backend deploy to Heroku / Render / AWS
* MongoDB Atlas for persistent storage
* Environment variables for API keys

---

## Acceptance Criteria (for MVP)

* Users can sign up and log in successfully
* Users can create, edit, view, and delete itineraries with trip types
* Places, hotels, and restaurants can be searched and added to itinerary
* Weather forecasts are shown for itinerary dates and locations
* Packing checklist adapts to trip type and weather
* Budget calculator returns a sensible total
* Notifications can be created and scheduled
* App is deployed and accessible via public URLs

---

## Timeline and Milestones (6–8 weeks, solo)

Week 1: Repo setup, backend skeleton, auth
Week 2: Frontend skeleton, auth pages, protected routes
Week 3: Itinerary model + CRUD APIs, initial UI
Week 4: Maps integration (places, hotels, restaurants), weather API
Week 5: Packing checklist, budget calculator, notifications
Week 6: Export (PDF/JSON), curated destinations, deployment
Week 7: Premium AI itinerary generator
Week 8: Testing, polish, documentation

---

## Future Enhancements

* AI-driven suggestions for entire trips
* Event discovery and integration
* Social sharing and collaboration features
* Full offline-first support with service workers
* Mobile app with native features

---

## Example folder structure

```
pack2go/
  server/
    package.json
    server.js
    routes/
    controllers/
    models/
    middleware/
    utils/
  client/
    package.json
    src/
      components/
      pages/
      services/
      styles/
  README.md
  SPEC.md
```

---

End of SPEC
