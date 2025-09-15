# Campus Nest - React Native Mobile App Architecture

## Overview
The Campus Nest React Native app will serve as the mobile companion to the web platform, providing students and hostel managers with a native mobile experience for hostel discovery and booking. The app will communicate with your FastAPI backend through REST APIs and offer optimized mobile UX for on-the-go hostel searching and booking management.

## App Architecture

### 1. Navigation Structure
```
📱 Campus Nest Mobile App
├── 🔐 Auth Stack (Unauthenticated)
│   ├── Welcome/Onboarding Screen
│   ├── Login Screen
│   ├── Register Screen (Student/Manager selection)
│   └── Password Reset Screen
│
├── 🏠 Student Tab Navigator (Authenticated - Student Role)
│   ├── Home Tab (Hostel Discovery)
│   ├── Search Tab (Advanced Filters)
│   ├── Bookings Tab (My Reservations)
│   └── Profile Tab (Account Settings)
│
├── 🏢 Manager Tab Navigator (Authenticated - Manager Role)
│   ├── Dashboard Tab (Overview)
│   ├── Hostels Tab (My Properties)
│   ├── Bookings Tab (Manage Reservations)
│   └── Profile Tab (Account Settings)
│
└── 📄 Modal Screens
    ├── Hostel Details Screen
    ├── Booking Confirmation Screen
    ├── Room Management Screen (Managers)
    └── Settings Screen
```

### 2. Key Components & Screens

#### Authentication Flow
- **Welcome Screen:** App intro with role selection (Student/Manager)
- **Login/Register:** Forms with validation and JWT token handling
- **Role-based Routing:** Navigate to appropriate tab navigator based on user role

#### Student Features
- **Home Screen:** Featured hostels, recent searches, quick filters
- **Search Screen:** Map view + list view, advanced filters (price, amenities, distance from campus)
- **Hostel Details:** Image carousel, amenities, pricing, room availability, booking CTA
- **Booking Flow:** Room selection → Date picker → Payment info → Confirmation
- **My Bookings:** List of active/past reservations with status tracking

#### Manager Features
- **Dashboard:** Booking analytics, revenue overview, occupancy rates
- **Manage Hostels:** Add/edit hostel details, upload photos, set pricing
- **Room Management:** Update room availability, pricing, amenities
- **Booking Management:** Approve/decline student requests, view booking details

### 3. State Management Architecture

#### Context/Redux Structure
```javascript
// App State Structure
{
  auth: {
    user: { id, email, role, profile },
    token: "jwt_token",
    isAuthenticated: boolean
  },
  hostels: {
    featured: [],
    searchResults: [],
    filters: { priceRange, amenities, location },
    loading: boolean
  },
  bookings: {
    studentBookings: [],
    managerBookings: [], // for managers
    currentBooking: {}
  },
  ui: {
    activeTab: string,
    notifications: [],
    loading: boolean
  }
}
```

#### Key Context Providers
- **AuthContext:** JWT management, user authentication state
- **HostelContext:** Hostel data, search filters, favorites
- **BookingContext:** Booking state management for both roles
- **LocationContext:** GPS location for distance-based search

### 4. API Integration Layer

#### API Service Structure
```javascript
// services/api.js
class CampusNestAPI {
  // Authentication
  login(credentials)
  register(userData)
  refreshToken()
  
  // Hostels (Students)
  getHostels(filters)
  getHostelById(id)
  searchHostels(query, location)
  
  // Bookings (Students)
  createBooking(hostelId, roomId, dates)
  getMyBookings()
  cancelBooking(bookingId)
  
  // Manager Operations
  getMyHostels()
  createHostel(hostelData)
  updateHostel(id, data)
  getHostelBookings(hostelId)
  approveBooking(bookingId)
  
  // File uploads
  uploadHostelImages(hostelId, images)
}
```

### 5. Key Mobile-Specific Features

#### Native Mobile Capabilities
- **Location Services:** GPS integration for "hostels near me"
- **Camera Integration:** Photo uploads for hostel managers
- **Push Notifications:** Booking confirmations, status updates
- **Offline Support:** Cache hostel data for offline browsing
- **Deep Linking:** Share hostel links, booking confirmations

#### Mobile UX Optimizations
- **Gesture Navigation:** Swipe gestures for image carousels
- **Pull-to-Refresh:** Update hostel listings and booking status
- **Infinite Scroll:** Load more hostels as user scrolls
- **Bottom Sheet Modals:** Mobile-friendly overlay screens
- **Haptic Feedback:** Touch feedback for booking actions

### 6. Database Integration (Mobile Side)

#### Local Storage Strategy
```javascript
// AsyncStorage for persistence
{
  // Authentication
  'auth_token': 'jwt_token',
  'user_profile': {...},
  
  // Cache
  'cached_hostels': [...],
  'search_history': [...],
  'favorite_hostels': [...],
  
  // Settings
  'app_preferences': {
    notifications: true,
    locationServices: true,
    theme: 'light'
  }
}
```

### 7. Screen Flow Examples

#### Student Booking Flow
1. **Home Screen** → Browse featured hostels
2. **Search Screen** → Apply filters, view map
3. **Hostel Details** → View photos, amenities, pricing
4. **Room Selection** → Choose room type and dates
5. **Booking Summary** → Review details, confirm
6. **Payment** → Process payment (future integration)
7. **Confirmation** → Booking success, add to "My Bookings"

#### Manager Hostel Creation
1. **Manager Dashboard** → Tap "Add Hostel"
2. **Basic Info** → Name, description, location
3. **Photos Upload** → Camera/gallery integration
4. **Room Details** → Add rooms, pricing, amenities
5. **Preview** → Review hostel listing
6. **Publish** → Make live for student bookings

### 8. Technical Considerations

#### Performance Optimization
- **Image Optimization:** Lazy loading, compressed images
- **API Caching:** Cache frequently accessed data
- **Bundle Optimization:** Code splitting for faster startup
- **Memory Management:** Efficient state updates

#### Security Measures
- **JWT Storage:** Secure token storage using Keychain/Keystore
- **API Security:** Request interceptors for token attachment
- **Input Validation:** Client-side validation before API calls
- **Biometric Auth:** Fingerprint/Face ID for quick login (future)

#### Cross-Platform Considerations
- **Platform-Specific UI:** Use Platform.select() for iOS/Android differences
- **Navigation:** React Navigation v6 for consistent routing
- **Styling:** Styled-components or React Native StyleSheet
- **Testing:** Jest + React Native Testing Library

## Conclusion

This React Native app will provide a seamless mobile experience that complements your web platform, allowing students to discover and book hostels on-the-go while enabling managers to efficiently manage their properties from their mobile devices.
