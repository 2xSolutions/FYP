# Smart Lens Mobile Application - Software Design Specification (SDS)

## Document Information
- **Project**: Smart Lens CCTV Surveillance System - Mobile Application
- **Version**: 1.0
- **Last Updated**: 2025-11-04

## Table of Contents
1. [Introduction](#1-introduction)
2. [Application Architecture](#2-application-architecture)
3. [Component Design](#3-component-design)
4. [Class Diagrams](#4-class-diagrams)
5. [User Interface Design](#5-user-interface-design)
6. [Data Management](#6-data-management)
7. [API Integration](#7-api-integration)
8. [State Management](#8-state-management)
9. [Security Design](#9-security-design)
10. [Deployment](#10-deployment)
11. [Appendices](#11-appendices)

---

## 1. Introduction

### 1.1 Purpose of Document
This Software Design Specification (SDS) provides a detailed technical design for the **Smart Lens Mobile Application**. It translates the requirements from the App SRS into a concrete architectural and implementation blueprint for the development team.

### 1.2 Scope
This document covers:
- Application architecture and design patterns
- Component and class structures
- UI/UX implementation details
- Data flow and state management
- API integration specifications
- Security implementation

### 1.3 Definitions and Acronyms
| Term | Definition |
|------|------------|
| BLoC | Business Logic Component |
| DTO | Data Transfer Object |
| MVVM | Model-View-ViewModel |
| REST | Representational State Transfer |
| JWT | JSON Web Token |
| FCM | Firebase Cloud Messaging |

---

## 2. Application Architecture

### 2.1 Architectural Pattern

The application follows a **Layered Architecture** with **Clean Architecture** principles:

```
┌─────────────────────────────────────────┐
│         Presentation Layer               │
│  (UI Widgets, Screens, ViewModels)      │
└──────────────┬──────────────────────────┘
               │
┌──────────────▼──────────────────────────┐
│         Application Layer                │
│  (Use Cases, Business Logic)            │
└──────────────┬──────────────────────────┘
               │
┌──────────────▼──────────────────────────┐
│         Domain Layer                     │
│  (Entities, Domain Models)              │
└──────────────┬──────────────────────────┘
               │
┌──────────────▼──────────────────────────┐
│         Data Layer                       │
│  (Repositories, API, Local Storage)     │
└─────────────────────────────────────────┘
               │
┌──────────────▼──────────────────────────┐
│     External Services                    │
│  (Backend API, FCM, Storage)            │
└─────────────────────────────────────────┘
```

### 2.2 Layer Responsibilities

#### Presentation Layer
- **Screens/Pages**: UI components and layouts
- **Widgets**: Reusable UI elements
- **ViewModels/Controllers**: Manage UI state and user interactions
- **Dependencies**: Application layer

#### Application Layer
- **Use Cases**: Implement business logic
- **Services**: Coordinate between UI and data
- **DTOs**: Data transfer objects
- **Dependencies**: Domain layer

#### Domain Layer
- **Entities**: Core business objects (User, Camera, Alert)
- **Repository Interfaces**: Abstract data access
- **Business Rules**: Domain-specific logic
- **Dependencies**: None (pure Dart)

#### Data Layer
- **Repository Implementations**: Concrete data access
- **API Client**: HTTP communication with backend
- **Local Storage**: Cache and persistent data
- **Dependencies**: External packages (dio, hive)

### 2.3 Folder Structure

```
lib/
├── main.dart
├── app/
│   ├── routes/
│   │   └── app_routes.dart
│   ├── theme/
│   │   └── app_theme.dart
│   └── config/
│       └── app_config.dart
├── core/
│   ├── constants/
│   ├── utils/
│   └── errors/
├── features/
│   ├── auth/
│   │   ├── data/
│   │   │   ├── models/
│   │   │   ├── repositories/
│   │   │   └── datasources/
│   │   ├── domain/
│   │   │   ├── entities/
│   │   │   ├── repositories/
│   │   │   └── usecases/
│   │   └── presentation/
│   │       ├── screens/
│   │       ├── widgets/
│   │       └── providers/
│   ├── cameras/
│   ├── alerts/
│   ├── live_feed/
│   └── settings/
└── shared/
    ├── widgets/
    ├── services/
    └── providers/
```

---

## 3. Component Design

### 3.1 Authentication Module

#### Components:
1. **LoginScreen**: Email/password input UI
2. **TwoFactorScreen**: OTP verification UI
3. **AuthService**: Authentication business logic
4. **AuthRepository**: API communication for auth
5. **SecureStorage**: Token storage management

#### Flow:
```
User Input → LoginScreen → AuthService → AuthRepository → Backend API
                                ↓
                         JWT Token Received
                                ↓
                         SecureStorage (save)
                                ↓
                         Navigate to Dashboard
```

#### Key Classes:

**AuthService**:
```dart
class AuthService {
  final AuthRepository _repository;
  final SecureStorage _storage;
  
  Future<LoginResult> login(String email, String password);
  Future<bool> verify2FA(String otp);
  Future<void> logout();
  Future<bool> isAuthenticated();
  Future<User?> getCurrentUser();
}
```

**AuthRepository**:
```dart
class AuthRepository {
  final ApiClient _apiClient;
  
  Future<LoginResponse> login(LoginRequest request);
  Future<OTPResponse> sendOTP(String email);
  Future<TokenResponse> verifyOTP(String email, String otp);
  Future<void> logout(String token);
}
```

### 3.2 Camera Management Module

#### Components:
1. **CameraListScreen**: Display all cameras
2. **AddCameraScreen**: Form for adding cameras
3. **EditCameraScreen**: Edit camera details
4. **CameraService**: Business logic
5. **CameraRepository**: API integration

#### Flow:
```
User Adds Camera → AddCameraScreen → Validate RTSP
                         ↓
                   CameraService (test connection)
                         ↓
                   CameraRepository (save to backend)
                         ↓
                   Update UI with new camera
```

#### Key Classes:

**Camera Entity**:
```dart
class Camera {
  final String id;
  final String name;
  final String rtspUrl;
  final String location;
  final CameraStatus status;
  final DateTime lastActive;
  
  Camera({
    required this.id,
    required this.name,
    required this.rtspUrl,
    required this.location,
    required this.status,
    required this.lastActive,
  });
}

enum CameraStatus { online, offline, error }
```

**CameraService**:
```dart
class CameraService {
  final CameraRepository _repository;
  
  Future<List<Camera>> getAllCameras();
  Future<Camera> addCamera(CreateCameraRequest request);
  Future<bool> testConnection(String rtspUrl);
  Future<void> updateCamera(String id, UpdateCameraRequest request);
  Future<void> deleteCamera(String id);
}
```

### 3.3 Alert Module

#### Components:
1. **AlertListScreen**: Display alerts
2. **AlertDetailScreen**: Show alert details with video
3. **AlertService**: Business logic
4. **AlertRepository**: API communication
5. **NotificationService**: FCM integration

#### Flow:
```
FCM Notification Received → NotificationService
                                    ↓
                          Parse notification payload
                                    ↓
                          Show in-app notification
                                    ↓
                          User taps notification
                                    ↓
                          Navigate to AlertDetailScreen
                                    ↓
                          Load alert from AlertRepository
                                    ↓
                          Display video and metadata
```

#### Key Classes:

**Alert Entity**:
```dart
class Alert {
  final String id;
  final String cameraId;
  final String cameraName;
  final String eventType;
  final double confidence;
  final DateTime timestamp;
  final String videoUrl;
  final String thumbnailUrl;
  final List<BoundingBox> boundingBoxes;
  
  Alert({
    required this.id,
    required this.cameraId,
    required this.cameraName,
    required this.eventType,
    required this.confidence,
    required this.timestamp,
    required this.videoUrl,
    required this.thumbnailUrl,
    this.boundingBoxes = const [],
  });
}

class BoundingBox {
  final int x, y, width, height;
  final String label;
  final double confidence;
}
```

**NotificationService**:
```dart
class NotificationService {
  final FirebaseMessaging _fcm;
  final AlertRepository _alertRepository;
  
  Future<void> initialize();
  Future<String> getDeviceToken();
  void handleForegroundMessage(RemoteMessage message);
  void handleBackgroundMessage(RemoteMessage message);
  void handleNotificationTap(RemoteMessage message);
  Future<void> updateDeviceToken(String token);
}
```

### 3.4 Live Feed Module

#### Components:
1. **LiveFeedDashboard**: Grid of camera previews
2. **FullScreenFeedScreen**: Full-screen video player
3. **VideoPlayerWidget**: Custom video player
4. **StreamService**: RTSP stream management

#### Key Classes:

**StreamService**:
```dart
class StreamService {
  final Map<String, StreamController> _activeStreams;
  
  Future<Stream<VideoFrame>> getStream(String cameraId, String rtspUrl);
  void pauseStream(String cameraId);
  void resumeStream(String cameraId);
  void stopStream(String cameraId);
  void stopAllStreams();
}
```

**VideoPlayerWidget**:
```dart
class VideoPlayerWidget extends StatefulWidget {
  final String videoUrl;
  final VideoType type; // rtsp, http, local
  final bool showControls;
  final Function(VideoPlayerController)? onPlayerReady;
  
  @override
  _VideoPlayerWidgetState createState() => _VideoPlayerWidgetState();
}
```

---

## 4. Class Diagrams

### 4.1 Domain Models

```
┌─────────────────────┐
│      User           │
├─────────────────────┤
│ - id: String        │
│ - email: String     │
│ - shopName: String  │
│ - phoneNumber: Str? │
│ - is2FAEnabled: bool│
└─────────────────────┘
          │
          │ owns
          ↓
┌─────────────────────┐
│      Camera         │
├─────────────────────┤
│ - id: String        │
│ - name: String      │
│ - rtspUrl: String   │
│ - location: String  │
│ - status: enum      │
│ - lastActive: Date  │
└─────────────────────┘
          │
          │ generates
          ↓
┌─────────────────────┐
│      Alert          │
├─────────────────────┤
│ - id: String        │
│ - cameraId: String  │
│ - eventType: String │
│ - confidence: float │
│ - timestamp: Date   │
│ - videoUrl: String  │
│ - thumbnail: String │
└─────────────────────┘
          │
          │ contains
          ↓
┌─────────────────────┐
│   EventClip         │
├─────────────────────┤
│ - id: String        │
│ - alertId: String   │
│ - duration: int     │
│ - fileSize: int     │
│ - localPath: String?│
│ - cloudUrl: String  │
└─────────────────────┘
```

### 4.2 Service Layer

```
┌──────────────────────┐
│   AuthService        │
├──────────────────────┤
│ + login()            │
│ + verify2FA()        │
│ + logout()           │
│ + isAuthenticated()  │
└──────────────────────┘
           │
           │ uses
           ↓
┌──────────────────────┐
│   AuthRepository     │
├──────────────────────┤
│ + login()            │
│ + sendOTP()          │
│ + verifyOTP()        │
└──────────────────────┘
           │
           │ calls
           ↓
┌──────────────────────┐
│   ApiClient          │
├──────────────────────┤
│ + get()              │
│ + post()             │
│ + put()              │
│ + delete()           │
└──────────────────────┘
```

---

## 5. User Interface Design

### 5.1 Screen Specifications

#### Login Screen
```dart
class LoginScreen extends StatefulWidget {
  @override
  _LoginScreenState createState() => _LoginScreenState();
}

class _LoginScreenState extends State<LoginScreen> {
  final _formKey = GlobalKey<FormState>();
  final _emailController = TextEditingController();
  final _passwordController = TextEditingController();
  
  Widget build(BuildContext context) {
    return Scaffold(
      body: SafeArea(
        child: Padding(
          padding: EdgeInsets.all(16.0),
          child: Form(
            key: _formKey,
            child: Column(
              mainAxisAlignment: MainAxisAlignment.center,
              children: [
                // Logo
                Image.asset('assets/logo.png', height: 100),
                SizedBox(height: 32),
                
                // Email field
                TextFormField(
                  controller: _emailController,
                  decoration: InputDecoration(
                    labelText: 'Email',
                    prefixIcon: Icon(Icons.email),
                  ),
                  validator: EmailValidator.validate,
                ),
                
                // Password field
                TextFormField(
                  controller: _passwordController,
                  obscureText: true,
                  decoration: InputDecoration(
                    labelText: 'Password',
                    prefixIcon: Icon(Icons.lock),
                  ),
                  validator: PasswordValidator.validate,
                ),
                
                // Login button
                ElevatedButton(
                  onPressed: _handleLogin,
                  child: Text('Login'),
                ),
                
                // Forgot password link
                TextButton(
                  onPressed: _handleForgotPassword,
                  child: Text('Forgot Password?'),
                ),
              ],
            ),
          ),
        ),
      ),
    );
  }
}
```

#### Dashboard Screen
```dart
class DashboardScreen extends StatefulWidget {
  @override
  _DashboardScreenState createState() => _DashboardScreenState();
}

class _DashboardScreenState extends State<DashboardScreen> {
  int _currentIndex = 0;
  
  final List<Widget> _screens = [
    LiveFeedScreen(),
    AlertListScreen(),
    CameraListScreen(),
    SettingsScreen(),
  ];
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: _screens[_currentIndex],
      bottomNavigationBar: BottomNavigationBar(
        currentIndex: _currentIndex,
        onTap: (index) => setState(() => _currentIndex = index),
        type: BottomNavigationBarType.fixed,
        items: [
          BottomNavigationBarItem(
            icon: Icon(Icons.videocam),
            label: 'Live Feed',
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.notifications),
            label: 'Alerts',
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.camera),
            label: 'Cameras',
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.settings),
            label: 'Settings',
          ),
        ],
      ),
    );
  }
}
```

### 5.2 Widget Components

#### Camera Card Widget
```dart
class CameraCard extends StatelessWidget {
  final Camera camera;
  final VoidCallback onTap;
  
  const CameraCard({
    required this.camera,
    required this.onTap,
  });
  
  @override
  Widget build(BuildContext context) {
    return Card(
      child: InkWell(
        onTap: onTap,
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            // Thumbnail/Preview
            AspectRatio(
              aspectRatio: 16 / 9,
              child: StreamPreview(
                cameraId: camera.id,
                rtspUrl: camera.rtspUrl,
              ),
            ),
            
            // Camera info
            Padding(
              padding: EdgeInsets.all(8.0),
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                children: [
                  Text(
                    camera.name,
                    style: TextStyle(
                      fontWeight: FontWeight.bold,
                      fontSize: 16,
                    ),
                  ),
                  SizedBox(height: 4),
                  Row(
                    children: [
                      Icon(
                        Icons.location_on,
                        size: 16,
                        color: Colors.grey,
                      ),
                      SizedBox(width: 4),
                      Text(
                        camera.location,
                        style: TextStyle(color: Colors.grey),
                      ),
                    ],
                  ),
                  SizedBox(height: 4),
                  StatusIndicator(status: camera.status),
                ],
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```

#### Alert Card Widget
```dart
class AlertCard extends StatelessWidget {
  final Alert alert;
  final VoidCallback onTap;
  
  const AlertCard({
    required this.alert,
    required this.onTap,
  });
  
  @override
  Widget build(BuildContext context) {
    return Card(
      margin: EdgeInsets.symmetric(horizontal: 16, vertical: 8),
      child: InkWell(
        onTap: onTap,
        child: Padding(
          padding: EdgeInsets.all(12),
          child: Row(
            children: [
              // Thumbnail
              ClipRRect(
                borderRadius: BorderRadius.circular(8),
                child: Image.network(
                  alert.thumbnailUrl,
                  width: 80,
                  height: 60,
                  fit: BoxFit.cover,
                ),
              ),
              SizedBox(width: 12),
              
              // Alert info
              Expanded(
                child: Column(
                  crossAxisAlignment: CrossAxisAlignment.start,
                  children: [
                    Row(
                      children: [
                        EventTypeChip(type: alert.eventType),
                        Spacer(),
                        Text(
                          '${(alert.confidence * 100).toInt()}%',
                          style: TextStyle(
                            color: Colors.grey,
                            fontSize: 12,
                          ),
                        ),
                      ],
                    ),
                    SizedBox(height: 4),
                    Text(
                      alert.cameraName,
                      style: TextStyle(
                        fontWeight: FontWeight.bold,
                      ),
                    ),
                    SizedBox(height: 2),
                    Text(
                      formatTimestamp(alert.timestamp),
                      style: TextStyle(
                        color: Colors.grey,
                        fontSize: 12,
                      ),
                    ),
                  ],
                ),
              ),
              
              // Arrow icon
              Icon(Icons.chevron_right),
            ],
          ),
        ),
      ),
    );
  }
}
```

---

## 6. Data Management

### 6.1 Local Storage Strategy

**Hive Database Structure**:

```dart
// User box
@HiveType(typeId: 0)
class UserModel {
  @HiveField(0)
  String id;
  
  @HiveField(1)
  String email;
  
  @HiveField(2)
  String shopName;
  
  @HiveField(3)
  bool is2FAEnabled;
}

// Camera box
@HiveType(typeId: 1)
class CameraModel {
  @HiveField(0)
  String id;
  
  @HiveField(1)
  String name;
  
  @HiveField(2)
  String rtspUrl;
  
  @HiveField(3)
  String location;
}

// Alert box (cached)
@HiveType(typeId: 2)
class AlertModel {
  @HiveField(0)
  String id;
  
  @HiveField(1)
  String eventType;
  
  @HiveField(2)
  String timestamp;
  
  @HiveField(3)
  String videoUrl;
}
```

### 6.2 Caching Strategy

**Cache Levels**:
1. **Memory Cache**: Active session data (5 minutes TTL)
2. **Disk Cache**: User data, camera list (24 hours TTL)
3. **Persistent Storage**: User preferences, downloaded clips

**Cache Invalidation**:
- Logout: Clear all caches
- Password change: Clear auth cache
- Camera update: Invalidate camera cache
- Pull-to-refresh: Force cache refresh

### 6.3 Data Synchronization

**Sync Strategy**:
```dart
class SyncService {
  Future<void> syncAll() async {
    await syncCameras();
    await syncAlerts();
    await syncSettings();
  }
  
  Future<void> syncCameras() async {
    final localCameras = await _localDataSource.getCameras();
    final remoteCameras = await _remoteDataSource.getCameras();
    
    // Compare and merge
    final merged = mergeCameras(localCameras, remoteCameras);
    
    // Update local storage
    await _localDataSource.saveCameras(merged);
  }
}
```

---

## 7. API Integration

### 7.1 API Client Implementation

```dart
class ApiClient {
  final Dio _dio;
  final SecureStorage _storage;
  
  ApiClient(this._dio, this._storage) {
    _dio.interceptors.add(
      InterceptorsWrapper(
        onRequest: (options, handler) async {
          // Add auth token
          final token = await _storage.getToken();
          if (token != null) {
            options.headers['Authorization'] = 'Bearer $token';
          }
          return handler.next(options);
        },
        onError: (error, handler) async {
          // Handle 401 Unauthorized
          if (error.response?.statusCode == 401) {
            await _handleUnauthorized();
          }
          return handler.next(error);
        },
      ),
    );
  }
  
  Future<Response<T>> get<T>(
    String path, {
    Map<String, dynamic>? queryParameters,
  }) async {
    try {
      return await _dio.get<T>(
        path,
        queryParameters: queryParameters,
      );
    } catch (e) {
      throw _handleError(e);
    }
  }
  
  Future<Response<T>> post<T>(
    String path, {
    dynamic data,
  }) async {
    try {
      return await _dio.post<T>(path, data: data);
    } catch (e) {
      throw _handleError(e);
    }
  }
}
```

### 7.2 API Endpoints

**Authentication**:
```dart
class AuthApiClient {
  final ApiClient _client;
  
  Future<LoginResponse> login(String email, String password) {
    return _client.post('/auth/login', data: {
      'email': email,
      'password': password,
    });
  }
  
  Future<TokenResponse> verify2FA(String email, String otp) {
    return _client.post('/auth/verify-2fa', data: {
      'email': email,
      'otp': otp,
    });
  }
}
```

**Cameras**:
```dart
class CameraApiClient {
  final ApiClient _client;
  
  Future<List<Camera>> getCameras() async {
    final response = await _client.get('/cameras');
    return (response.data as List)
        .map((json) => Camera.fromJson(json))
        .toList();
  }
  
  Future<Camera> createCamera(CreateCameraRequest request) async {
    final response = await _client.post(
      '/cameras',
      data: request.toJson(),
    );
    return Camera.fromJson(response.data);
  }
}
```

**Alerts**:
```dart
class AlertApiClient {
  final ApiClient _client;
  
  Future<List<Alert>> getAlerts({
    String? eventType,
    String? cameraId,
    DateTime? startDate,
    DateTime? endDate,
  }) async {
    final response = await _client.get('/alerts', queryParameters: {
      'event_type': eventType,
      'camera_id': cameraId,
      'start_date': startDate?.toIso8601String(),
      'end_date': endDate?.toIso8601String(),
    });
    return (response.data as List)
        .map((json) => Alert.fromJson(json))
        .toList();
  }
  
  Future<void> deleteAlert(String alertId) async {
    await _client.delete('/alerts/$alertId');
  }
}
```

---

## 8. State Management

### 8.1 Provider Pattern

**AuthProvider**:
```dart
class AuthProvider extends ChangeNotifier {
  final AuthService _authService;
  
  User? _user;
  AuthState _state = AuthState.unauthenticated;
  
  User? get user => _user;
  AuthState get state => _state;
  bool get isAuthenticated => _state == AuthState.authenticated;
  
  Future<void> login(String email, String password) async {
    _state = AuthState.loading;
    notifyListeners();
    
    try {
      await _authService.login(email, password);
      _state = AuthState.pending2FA;
      notifyListeners();
    } catch (e) {
      _state = AuthState.error;
      notifyListeners();
      throw e;
    }
  }
  
  Future<void> verify2FA(String otp) async {
    try {
      final user = await _authService.verify2FA(otp);
      _user = user;
      _state = AuthState.authenticated;
      notifyListeners();
    } catch (e) {
      _state = AuthState.error;
      notifyListeners();
      throw e;
    }
  }
}

enum AuthState {
  unauthenticated,
  loading,
  pending2FA,
  authenticated,
  error,
}
```

**CameraProvider**:
```dart
class CameraProvider extends ChangeNotifier {
  final CameraService _cameraService;
  
  List<Camera> _cameras = [];
  bool _isLoading = false;
  String? _error;
  
  List<Camera> get cameras => _cameras;
  bool get isLoading => _isLoading;
  String? get error => _error;
  
  Future<void> loadCameras() async {
    _isLoading = true;
    _error = null;
    notifyListeners();
    
    try {
      _cameras = await _cameraService.getAllCameras();
      _isLoading = false;
      notifyListeners();
    } catch (e) {
      _error = e.toString();
      _isLoading = false;
      notifyListeners();
    }
  }
  
  Future<void> addCamera(CreateCameraRequest request) async {
    final camera = await _cameraService.addCamera(request);
    _cameras.add(camera);
    notifyListeners();
  }
  
  Future<void> deleteCamera(String id) async {
    await _cameraService.deleteCamera(id);
    _cameras.removeWhere((c) => c.id == id);
    notifyListeners();
  }
}
```

**AlertProvider**:
```dart
class AlertProvider extends ChangeNotifier {
  final AlertService _alertService;
  final NotificationService _notificationService;
  
  List<Alert> _alerts = [];
  int _unreadCount = 0;
  
  List<Alert> get alerts => _alerts;
  int get unreadCount => _unreadCount;
  
  AlertProvider(this._alertService, this._notificationService) {
    _notificationService.onNotificationReceived.listen(_handleNewAlert);
  }
  
  void _handleNewAlert(Alert alert) {
    _alerts.insert(0, alert);
    _unreadCount++;
    notifyListeners();
  }
  
  Future<void> loadAlerts({
    String? eventType,
    String? cameraId,
  }) async {
    _alerts = await _alertService.getAlerts(
      eventType: eventType,
      cameraId: cameraId,
    );
    notifyListeners();
  }
  
  void markAsRead(String alertId) {
    _unreadCount = max(0, _unreadCount - 1);
    notifyListeners();
  }
}
```

---

## 9. Security Design

### 9.1 Secure Token Storage

```dart
class SecureStorage {
  final FlutterSecureStorage _storage;
  
  Future<void> saveToken(String token) async {
    await _storage.write(
      key: 'auth_token',
      value: token,
    );
  }
  
  Future<String?> getToken() async {
    return await _storage.read(key: 'auth_token');
  }
  
  Future<void> deleteToken() async {
    await _storage.delete(key: 'auth_token');
  }
}
```

### 9.2 Input Validation

```dart
class Validators {
  static String? email(String? value) {
    if (value == null || value.isEmpty) {
      return 'Email is required';
    }
    final emailRegex = RegExp(r'^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$');
    if (!emailRegex.hasMatch(value)) {
      return 'Invalid email format';
    }
    return null;
  }
  
  static String? password(String? value) {
    if (value == null || value.isEmpty) {
      return 'Password is required';
    }
    if (value.length < 8) {
      return 'Password must be at least 8 characters';
    }
    if (!value.contains(RegExp(r'[A-Z]'))) {
      return 'Password must contain uppercase letter';
    }
    if (!value.contains(RegExp(r'[0-9]'))) {
      return 'Password must contain a number';
    }
    return null;
  }
  
  static String? rtspUrl(String? value) {
    if (value == null || value.isEmpty) {
      return 'RTSP URL is required';
    }
    if (!value.startsWith('rtsp://')) {
      return 'URL must start with rtsp://';
    }
    return null;
  }
}
```

### 9.3 Encryption

```dart
class EncryptionService {
  static String encrypt(String data, String key) {
    // AES-256 encryption implementation
    final encrypter = Encrypter(AES(Key.fromUtf8(key)));
    final encrypted = encrypter.encrypt(data, iv: IV.fromLength(16));
    return encrypted.base64;
  }
  
  static String decrypt(String encryptedData, String key) {
    final encrypter = Encrypter(AES(Key.fromUtf8(key)));
    final decrypted = encrypter.decrypt64(encryptedData, iv: IV.fromLength(16));
    return decrypted;
  }
}
```

---

## 10. Deployment

### 10.1 Build Configuration

**Android Build (build.gradle)**:
```gradle
android {
    compileSdkVersion 33
    defaultConfig {
        applicationId "com.smartlens.app"
        minSdkVersion 26
        targetSdkVersion 33
        versionCode 1
        versionName "1.0.0"
    }
    
    buildTypes {
        release {
            minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}
```

### 10.2 Environment Configuration

```dart
class AppConfig {
  static const String apiBaseUrl = 
      String.fromEnvironment('API_URL', defaultValue: 'https://api.smartlens.com');
  
  static const bool isProduction = 
      bool.fromEnvironment('PRODUCTION', defaultValue: false);
      
  static const String fcmSenderId = 
      String.fromEnvironment('FCM_SENDER_ID');
}
```

### 10.3 Release Checklist

- [ ] Code obfuscation enabled
- [ ] API keys secured
- [ ] SSL certificate pinning
- [ ] Crashlytics integrated
- [ ] Analytics configured
- [ ] Performance monitoring
- [ ] App signing configured
- [ ] Privacy policy included
- [ ] Terms of service included

---

## 11. Appendices

### Appendix A: Dependencies

**pubspec.yaml**:
```yaml
dependencies:
  flutter:
    sdk: flutter
  
  # State Management
  provider: ^6.0.5
  
  # Networking
  dio: ^5.3.0
  
  # Local Storage
  hive: ^2.2.3
  hive_flutter: ^1.1.0
  flutter_secure_storage: ^8.0.0
  
  # Video Player
  video_player: ^2.7.0
  chewie: ^1.5.0
  
  # Push Notifications
  firebase_core: ^2.15.0
  firebase_messaging: ^14.6.5
  
  # UI
  cached_network_image: ^3.2.3
  shimmer: ^3.0.0
  
  # Utils
  intl: ^0.18.1
  path_provider: ^2.0.15
```

### Appendix B: Testing Strategy

**Unit Tests**: Test business logic and services
**Widget Tests**: Test UI components
**Integration Tests**: Test complete user flows

Example:
```dart
testWidgets('Login screen shows error for invalid credentials', 
  (WidgetTester tester) async {
    await tester.pumpWidget(MyApp());
    
    await tester.enterText(find.byKey(Key('email')), 'test@test.com');
    await tester.enterText(find.byKey(Key('password')), 'wrong');
    await tester.tap(find.byKey(Key('loginButton')));
    await tester.pumpAndSettle();
    
    expect(find.text('Invalid credentials'), findsOneWidget);
});
```

---

## Document Approval

| Role | Name | Signature | Date |
|------|------|-----------|------|
| Mobile Architect | _______________ | _______________ | ______ |
| Lead Developer | _______________ | _______________ | ______ |
| Project Supervisor | _______________ | _______________ | ______ |

---

**End of Document**
