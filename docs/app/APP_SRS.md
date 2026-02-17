# Smart Lens Mobile Application - Software Requirements Specification (SRS)

## Document Information
- **Project**: Smart Lens CCTV Surveillance System - Mobile Application
- **Version**: 1.0
- **Last Updated**: 2025-11-04

## Table of Contents
1. [Introduction](#1-introduction)
2. [Application Overview](#2-application-overview)
3. [Functional Requirements](#3-functional-requirements)
4. [Non-Functional Requirements](#4-non-functional-requirements)
5. [User Interface Requirements](#5-user-interface-requirements)
6. [Integration Requirements](#6-integration-requirements)
7. [Security Requirements](#7-security-requirements)
8. [Appendices](#8-appendices)

---

## 1. Introduction

### 1.1 Purpose of Document
This Software Requirements Specification (SRS) document defines the requirements for the **Mobile Application** component of the Smart Lens CCTV Surveillance System. The application provides the user interface for shopkeepers to monitor cameras, receive alerts, and manage their surveillance system.

### 1.2 Scope
The Smart Lens Mobile Application is a cross-platform mobile app that:
- Provides user authentication and account management
- Enables camera registration and management
- Displays live camera feeds
- Receives and displays security alerts
- Allows users to view, download, and share recorded event clips
- Manages notification preferences and third-party alert forwarding
- Operates independently from the AI model layer

### 1.3 Intended Audience
- Mobile App Development Team
- UI/UX Designers
- Backend Integration Engineers
- Testing and QA Team
- End Users (Shopkeepers)

### 1.4 Definitions and Acronyms
| Term | Definition |
|------|------------|
| UI | User Interface |
| UX | User Experience |
| 2FA | Two-Factor Authentication |
| FCM | Firebase Cloud Messaging |
| JWT | JSON Web Token |
| RTSP | Real-Time Streaming Protocol |
| OTP | One-Time Password |

---

## 2. Application Overview

### 2.1 Application Purpose
The Smart Lens Mobile Application serves as the primary interface for shopkeepers to interact with their AI-powered surveillance system, providing real-time monitoring, instant threat notifications, and event management capabilities.

### 2.2 Target Users
- **Primary Users**: Small business owners and shopkeepers
- **User Expertise**: Limited technical knowledge
- **Platform**: Android smartphones (iOS support future consideration)
- **Age Range**: 25-65 years
- **Language**: English (with potential for local language support)

### 2.3 Key Features
1. **Secure Authentication**: Email/password login with mandatory 2FA
2. **Camera Management**: Add, edit, and remove IP cameras
3. **Live Monitoring**: View real-time feeds from multiple cameras
4. **Alert Reception**: Receive instant push notifications for threats
5. **Event Review**: Browse, search, and filter recorded event clips
6. **Alert Sharing**: Forward alerts to trusted contacts or authorities
7. **Profile Management**: Update account settings and preferences

---

## 3. Functional Requirements

### FR-A1: User Authentication and Account Management

**FR-A1.1**: The app shall allow new users to register an account with:
- Email address (validated format)
- Password (minimum 8 characters, including uppercase, lowercase, number)
- Shop name
- Phone number (optional)

**FR-A1.2**: The app shall implement mandatory Two-Factor Authentication (2FA):
- Send 6-digit OTP to registered email during login
- OTP validity: 5 minutes
- Maximum 3 OTP attempts before lockout

**FR-A1.3**: The app shall provide secure login using:
- Email and password authentication
- JWT token-based session management
- Automatic session expiration after 24 hours of inactivity

**FR-A1.4**: The app shall allow users to reset forgotten passwords via:
- Email verification link
- Temporary password sent to registered email
- Force password change on first login after reset

**FR-A1.5**: The app shall enable users to update profile information:
- Shop name
- Contact phone number
- Notification preferences
- Alert recipient list

**FR-A1.6**: The app shall allow users to delete their account:
- Require password confirmation
- Show warning about data deletion
- Permanently remove all user data and camera configurations

**FR-A1.7**: The app shall maintain secure session state:
- Store JWT token securely in device keychain
- Validate token on app launch
- Automatic logout on token expiration
- Invalidate token on password change

### FR-A2: Camera Management

**FR-A2.1**: The app shall allow users to add new cameras by providing:
- Camera name/label (e.g., "Front Door", "Cash Register")
- RTSP stream URL
- Location description
- Optional notes

**FR-A2.2**: The app shall validate camera connectivity before saving:
- Test RTSP connection
- Display connection status (success/failed)
- Show error message for invalid URLs

**FR-A2.3**: The app shall allow users to edit existing camera details:
- Update camera name/label
- Modify location description
- Edit notes
- Cannot modify RTSP URL (requires removal and re-add)

**FR-A2.4**: The app shall allow users to remove cameras:
- Show confirmation dialog
- Warn about associated event deletion
- Remove camera and all related data

**FR-A2.5**: The app shall display a list of all registered cameras showing:
- Camera name
- Location
- Connection status (online/offline)
- Last active timestamp

**FR-A2.6**: The app shall support up to 10 cameras per user account.

### FR-A3: Live Feed Monitoring

**FR-A3.1**: The app shall provide a dashboard displaying:
- Grid view of all camera thumbnails
- Live feed previews (refreshed every 2-3 seconds)
- Camera status indicators (online/offline)
- Quick access to full-screen view

**FR-A3.2**: The app shall allow users to view full-screen live feed for any camera:
- Tap camera thumbnail to expand
- Display camera name and location
- Show current timestamp
- Provide zoom and pan controls

**FR-A3.3**: The app shall support multi-camera view:
- Display up to 4 cameras simultaneously
- Auto-layout based on number of active cameras
- Swipe to switch between camera groups

**FR-A3.4**: The app shall handle streaming errors gracefully:
- Display "Connection Lost" message
- Show retry button
- Auto-reconnect on network restoration

**FR-A3.5**: The app shall display live feed quality based on network:
- Auto-adjust resolution (480p, 720p, 1080p)
- Show current quality indicator
- Allow manual quality selection

### FR-A4: Alert and Notification System

**FR-A4.1**: The app shall receive instant push notifications for detected threats:
- Notification title: "Smart Lens Security Alert!"
- Event type (Theft, Violence, Fire, Weapon)
- Camera location
- Timestamp
- Thumbnail image (if available)

**FR-A4.2**: The app shall support notification actions:
- Tap notification to open event detail
- Quick action buttons: "View", "Dismiss", "Forward"
- Badge counter for unread alerts

**FR-A4.3**: The app shall maintain an Alert History showing:
- List of all received alerts (newest first)
- Filter by event type
- Filter by camera
- Filter by date range
- Search by keyword

**FR-A4.4**: The app shall display detailed alert information:
- Event type and confidence score
- Detection timestamp
- Camera name and location
- Embedded video clip player
- Bounding boxes on detected objects (if available)

**FR-A4.5**: The app shall allow users to manage notification preferences:
- Enable/disable push notifications
- Select which event types to receive
- Set quiet hours (no notifications)
- Configure notification sound

**FR-A4.6**: The app shall provide in-app notification center:
- Unread alerts badge
- Real-time alert updates
- Notification history

### FR-A5: Event Recording and Playback

**FR-A5.1**: The app shall display a library of recorded event clips:
- Thumbnail previews
- Event type labels
- Duration and file size
- Camera source
- Recording timestamp

**FR-A5.2**: The app shall provide video playback controls:
- Play/pause
- Seek/scrub timeline
- Playback speed (0.5x, 1x, 1.5x, 2x)
- Fullscreen mode

**FR-A5.3**: The app shall allow users to download event clips:
- Save to device gallery
- Show download progress
- Confirm successful download

**FR-A5.4**: The app shall allow users to delete event clips:
- Single clip deletion
- Bulk deletion with selection
- Confirmation dialog before deletion
- Free up storage space indicator

**FR-A5.5**: The app shall provide search and filter options:
- Filter by event type
- Filter by camera
- Filter by date range
- Sort by newest/oldest

**FR-A5.6**: The app shall display storage usage:
- Total clips stored
- Total storage consumed
- Available storage remaining
- Warning when storage limit approaching

### FR-A6: Alert Forwarding and Sharing

**FR-A6.1**: The app shall allow users to forward alerts to external parties:
- Email
- SMS/WhatsApp
- Share via social media (optional)

**FR-A6.2**: The app shall enable users to configure trusted recipients:
- Add up to 5 recipient contacts
- Store name, email, and phone number
- Set default recipients for auto-forwarding

**FR-A6.3**: The app shall provide forwarding options:
- Forward individual alert with video clip link
- Forward alert summary without video
- Include custom message
- Track sent status

**FR-A6.4**: The app shall support emergency alert forwarding:
- Quick access button on alert detail screen
- Pre-configured message template
- Send to all trusted contacts simultaneously
- Optional: Forward to local law enforcement (future)

### FR-A7: Settings and Preferences

**FR-A7.1**: The app shall provide account settings:
- View and edit profile information
- Change password
- Enable/disable 2FA
- Delete account

**FR-A7.2**: The app shall provide notification settings:
- Push notification toggle
- Event type filters
- Quiet hours configuration
- Notification sound selection

**FR-A7.3**: The app shall provide video playback settings:
- Default video quality
- Auto-play next clip
- Background playback

**FR-A7.4**: The app shall provide data management settings:
- Clear cache
- Auto-delete old clips (configure retention period)
- Storage location preference

**FR-A7.5**: The app shall provide app information:
- App version
- Terms of service
- Privacy policy
- Contact support

### FR-A8: Search and Filtering

**FR-A8.1**: The app shall provide global search functionality:
- Search alerts by keyword
- Search cameras by name
- Search event clips by date

**FR-A8.2**: The app shall support advanced filtering:
- Combine multiple filters (event type + camera + date)
- Save filter presets
- Clear all filters option

**FR-A8.3**: The app shall provide sorting options:
- Sort by date (newest/oldest)
- Sort by event type
- Sort by camera
- Sort by confidence score

---

## 4. Non-Functional Requirements

### NFR-A1: Performance Requirements

**NFR-A1.1**: The app shall load the dashboard within 2 seconds on app launch.

**NFR-A1.2**: The app shall display live feed within 5 seconds of selection on stable internet.

**NFR-A1.3**: The app shall receive push notifications within 30 seconds of threat detection.

**NFR-A1.4**: The app shall support smooth video playback at 15+ FPS for recorded clips.

**NFR-A1.5**: The app shall handle up to 100 alert notifications without performance degradation.

**NFR-A1.6**: The app shall maintain responsive UI with maximum 100ms touch response time.

### NFR-A2: Usability Requirements

**NFR-A2.1**: The app shall be designed for users with limited technical expertise:
- Simple, intuitive navigation
- Clear icons and labels
- Minimal text input required
- Visual feedback for all actions

**NFR-A2.2**: The app shall provide contextual help:
- Tooltips for complex features
- First-time user tutorial
- Help button on each screen

**NFR-A2.3**: The app shall support accessibility features:
- Minimum font size 14pt
- High contrast mode
- Screen reader compatibility
- Large touch targets (minimum 44x44 points)

**NFR-A2.4**: The app shall provide clear error messages:
- User-friendly language
- Actionable suggestions
- No technical jargon

**NFR-A2.5**: The app shall maintain consistency:
- Consistent color scheme
- Consistent button styles
- Consistent navigation patterns

### NFR-A3: Reliability Requirements

**NFR-A3.1**: The app shall maintain 99% uptime under normal operating conditions.

**NFR-A3.2**: The app shall handle network interruptions gracefully:
- Auto-reconnect when network restored
- Queue notifications during offline periods
- Display offline mode indicator

**NFR-A3.3**: The app shall prevent data loss:
- Auto-save form inputs
- Confirm before discarding changes
- Sync data with backend regularly

**NFR-A3.4**: The app shall recover from crashes:
- Automatic crash reporting
- Restore previous session state
- Preserve unsaved data where possible

### NFR-A4: Security Requirements

**NFR-A4.1**: The app shall encrypt all data transmission using HTTPS/TLS.

**NFR-A4.2**: The app shall store sensitive data securely:
- JWT tokens in device keychain/keystore
- Encrypted local database for cached data
- No plaintext password storage

**NFR-A4.3**: The app shall implement session security:
- Auto-logout after 24 hours inactivity
- Force re-authentication for sensitive operations
- Invalidate sessions on password change

**NFR-A4.4**: The app shall protect against common vulnerabilities:
- Input validation and sanitization
- SQL injection prevention
- XSS protection
- CSRF protection

**NFR-A4.5**: The app shall implement biometric authentication (optional):
- Fingerprint login
- Face ID login
- Fallback to password if biometric fails

### NFR-A5: Compatibility Requirements

**NFR-A5.1**: The app shall support Android versions 8.0 (API 26) and above.

**NFR-A5.2**: The app shall be compatible with various screen sizes:
- Phone: 4.5" to 7" screens
- Tablet: 7" to 12" screens (responsive layout)

**NFR-A5.3**: The app shall function on devices with minimum specifications:
- 2GB RAM
- 100MB available storage
- Android 8.0+

**NFR-A5.4**: The app shall support various network conditions:
- 3G, 4G, 5G, WiFi
- Adaptive quality based on bandwidth
- Offline mode for cached content

### NFR-A6: Maintainability Requirements

**NFR-A6.1**: The app codebase shall follow Flutter best practices:
- Clean architecture pattern
- Modular component structure
- Comprehensive code documentation

**NFR-A6.2**: The app shall support over-the-air updates:
- Auto-update check on launch
- Notify user of available updates
- Force update for critical patches

**NFR-A6.3**: The app shall include comprehensive logging:
- User action logs (anonymized)
- Error logs with stack traces
- Performance metrics

### NFR-A7: Localization Requirements

**NFR-A7.1**: The app shall support multiple languages (future):
- English (default)
- Urdu (planned)
- Other local languages as needed

**NFR-A7.2**: The app shall use localized date/time formats.

**NFR-A7.3**: The app shall support right-to-left (RTL) layouts for applicable languages.

---

## 5. User Interface Requirements

### 5.1 Navigation Structure

```
App Launch
    ↓
[Authentication Required?]
    ├─ Yes → Login Screen → 2FA Verification → Dashboard
    └─ No → Dashboard (if valid session)

Dashboard
    ├─ Live Feeds Tab
    ├─ Alerts Tab
    ├─ Cameras Tab
    └─ Settings Tab
```

### 5.2 Screen Requirements

#### Screen 1: Login & 2FA
- Email and password input fields
- "Remember me" checkbox
- "Forgot Password?" link
- Login button
- OTP input screen (6-digit code)
- "Resend OTP" button

#### Screen 2: Dashboard (Home)
- Navigation tabs (bottom)
- Alert badge counter
- Quick stats: Cameras online, Recent alerts
- Camera grid view with live previews
- Floating action button for adding camera

#### Screen 3: Live Feed
- Full-screen video player
- Camera name and location header
- Timestamp overlay
- Quality indicator
- Zoom/pan controls
- Back button

#### Screen 4: Alerts
- List of alerts (card-based)
- Filter buttons (event type, camera, date)
- Search bar
- Pull-to-refresh
- Empty state message

#### Screen 5: Alert Detail
- Video player with event clip
- Event metadata (type, confidence, time, camera)
- Action buttons: Download, Delete, Forward
- Bounding boxes overlay (if available)
- Related alerts section

#### Screen 6: Cameras
- List of registered cameras
- Add camera button
- Each camera shows: Name, Location, Status
- Swipe actions: Edit, Delete
- Empty state with "Add Camera" prompt

#### Screen 7: Add/Edit Camera
- Camera name input
- RTSP URL input
- Location input
- Notes input (optional)
- Test connection button
- Save/Cancel buttons

#### Screen 8: Settings
- Profile section
- Notification preferences
- Video settings
- Data management
- About & Help
- Logout button

### 5.3 Design Guidelines

**Color Scheme**:
- Primary: Blue (#2196F3) - Trust, security
- Accent: Orange (#FF9800) - Alerts, warnings
- Error: Red (#F44336) - Danger, critical alerts
- Success: Green (#4CAF50) - Positive actions
- Background: White/Light Gray (#FAFAFA)
- Text: Dark Gray (#212121)

**Typography**:
- Headings: Bold, 20-24pt
- Body: Regular, 14-16pt
- Captions: Regular, 12-14pt
- Font Family: Roboto (Android)

**Icons**:
- Material Design Icons
- Minimum size: 24x24 dp
- Clear, recognizable symbols

**Spacing**:
- Padding: 16dp standard
- Margin: 8dp between elements
- Card elevation: 2-4dp

---

## 6. Integration Requirements

### 6.1 Backend API Integration

The app shall integrate with the backend server via REST APIs:

**Base URL**: `https://api.smartlens.com`

**Authentication**: JWT Bearer token in header

**Key Endpoints**:
- `POST /auth/register` - User registration
- `POST /auth/login` - User login
- `POST /auth/verify-2fa` - 2FA verification
- `GET /cameras` - List user cameras
- `POST /cameras` - Add new camera
- `PUT /cameras/{id}` - Update camera
- `DELETE /cameras/{id}` - Remove camera
- `GET /alerts` - List alerts
- `GET /alerts/{id}` - Get alert details
- `DELETE /alerts/{id}` - Delete alert
- `POST /alerts/{id}/forward` - Forward alert
- `GET /clips` - List event clips
- `GET /clips/{id}/download` - Download clip

### 6.2 Push Notification Integration

**Service**: Firebase Cloud Messaging (FCM)

**Implementation**:
- Register device token on app install
- Update token on app launch
- Handle notification in foreground and background
- Parse notification payload for alert data
- Deep link to alert detail screen

### 6.3 Video Streaming Integration

**Protocol**: RTSP for live feeds, HLS/DASH for recorded clips

**Libraries**:
- ExoPlayer (Android) for video playback
- VLC for RTSP streaming
- Custom video player controls

### 6.4 Model Integration

The app does not directly integrate with the AI model. All model interactions are mediated through the backend API:

```
App → Backend API → AI Model
App ← Backend API ← AI Model (detection events)
```

---

## 7. Security Requirements

### 7.1 Data Protection

**SR-A1**: All user credentials shall be transmitted over HTTPS.

**SR-A2**: JWT tokens shall be stored in platform-specific secure storage (Keychain/Keystore).

**SR-A3**: Local cache data shall be encrypted using AES-256.

**SR-A4**: Video clips downloaded to device shall be stored in app-specific encrypted storage.

### 7.2 Authentication Security

**SR-A5**: Passwords shall meet complexity requirements (min 8 chars, mixed case, numbers).

**SR-A6**: Failed login attempts shall be rate-limited (max 5 attempts per hour).

**SR-A7**: 2FA OTP codes shall expire after 5 minutes.

**SR-A8**: Sessions shall automatically expire after 24 hours of inactivity.

### 7.3 Authorization Security

**SR-A9**: Users shall only access their own cameras and alerts.

**SR-A10**: All API requests shall include valid JWT token.

**SR-A11**: Expired tokens shall be rejected with 401 Unauthorized.

**SR-A12**: Sensitive operations (delete account) shall require password re-entry.

### 7.4 Privacy Requirements

**SR-A13**: The app shall not collect unnecessary personal data.

**SR-A14**: User consent shall be required for push notifications.

**SR-A15**: Video clips shall not be shared with third parties without user consent.

**SR-A16**: The app shall comply with GDPR and local privacy regulations.

**SR-A17**: Users shall have the right to export their data.

**SR-A18**: Users shall have the right to delete all their data.

---

## 8. Appendices

### Appendix A: User Personas

**Persona 1: Small Shop Owner**
- Name: Ahmed, 45 years old
- Location: Karachi, Pakistan
- Technical expertise: Low
- Needs: Simple interface, instant alerts, reliable monitoring
- Concerns: Cost, ease of use, false alarms

**Persona 2: Market Vendor**
- Name: Fatima, 32 years old
- Location: Lahore, Pakistan
- Technical expertise: Moderate
- Needs: Mobile access, quick setup, sharing alerts with family
- Concerns: Data privacy, internet reliability

### Appendix B: Use Case Scenarios

**Scenario 1: First-Time Setup**
1. User downloads app from Play Store
2. User registers account with email/password
3. User verifies email via 2FA
4. User adds first camera via RTSP URL
5. User tests live feed
6. User configures notification preferences

**Scenario 2: Receiving Threat Alert**
1. AI model detects theft in camera feed
2. Backend sends push notification to user's phone
3. User taps notification
4. App opens alert detail screen with video clip
5. User reviews video and decides to forward to police
6. User taps "Forward" and selects recipient
7. Alert sent via email with video link

**Scenario 3: Reviewing Past Events**
1. User opens app and navigates to Alerts tab
2. User filters alerts by "Fire" event type
3. User selects date range (last 7 days)
4. App displays filtered results
5. User taps on an alert to view details
6. User downloads video clip for insurance claim

### Appendix C: Technology Stack

**Framework**: Flutter 3.10+
**Language**: Dart
**State Management**: Provider or Riverpod
**Networking**: Dio (HTTP client)
**Local Storage**: Hive or SQLite
**Video Player**: video_player or better_player
**Push Notifications**: firebase_messaging
**Authentication**: firebase_auth (optional) or custom JWT
**UI Components**: Material Design

### Appendix D: Testing Requirements

**Unit Tests**: 80% code coverage minimum
**Widget Tests**: All custom widgets
**Integration Tests**: Critical user flows
**Performance Tests**: Load time, memory usage
**Security Tests**: Penetration testing, vulnerability scanning
**Usability Tests**: User acceptance testing with target users

---

## Document Approval

| Role | Name | Signature | Date |
|------|------|-----------|------|
| Mobile App Lead | _______________ | _______________ | ______ |
| UI/UX Designer | _______________ | _______________ | ______ |
| Project Supervisor | _______________ | _______________ | ______ |

---

**End of Document**
