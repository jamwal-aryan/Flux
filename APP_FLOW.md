# APP_FLOW.md

**Application:** Flux
**Type:** Desktop Video Frame Interpolation Tool
**Purpose:** Convert low-frame-rate videos into smoother high-frame-rate videos using frame interpolation techniques.

---

# 1. ENTRY POINTS

### 1. Direct Application Launch

Primary entry.

* User launches Flux executable or command
* Application opens **Home / Video Upload Screen**

---

### 2. Drag-and-Drop File Launch

User drags a video file onto the Flux application icon.

System behavior:

* Application opens automatically
* Video automatically loaded
* User taken to **Processing Setup Screen**

---

### 3. File Association Launch

If user double-clicks supported file types.

Supported types:

* MP4
* AVI
* MOV

System behavior:

* Flux launches
* File automatically loaded
* User navigated to **Processing Setup Screen**

---

### 4. Command-Line Launch (Developer Mode)

Example:

```
flux input.mp4 --target-fps 60
```

System behavior:

* Flux loads video
* Processing begins automatically

---

# 2. CORE USER FLOWS

---

# FLOW 1 — Application Start & Video Import

---

## HAPPY PATH

### Step 1 — Home Screen

**Page:** Home / Upload Screen

**UI Elements**

* Upload Video Button
* Drag & Drop Zone with a **Browse** button within (as an alternative to Drag & Drop)
* Supported Format Info
* App Header
* Settings Icon

**User Actions**

* Click Upload
* Drag video file into window OR Click **Browse** button within the Drag & Drop Zone and select a video file

**System Response**

* File validation begins
* Video metadata extracted

**Validation Rules**

* File format must be MP4, AVI, MOV
* File size ≤ 2GB

**Next State**

→ Processing Setup Screen

**Success Criteria**

* Video loaded successfully
* Metadata displayed

---

### Step 2 — Processing Setup Screen

**Page:** Interpolation Settings

**UI Elements**

* Video preview
* Video metadata panel
  * FPS
  * Resolution
  * Duration
* Interpolation Mode Selector
* Target FPS Selector
* Preview Button
* Start Processing Button

**User Actions**

* Choose interpolation mode
* Select target FPS
* Start processing

**System Response**

* Configuration validated
* Processing pipeline initialized

**Validation Rules**

* Target FPS must be greater than input FPS

**Next State**

→ Processing Screen

---

### Step 3 — Processing Screen

**Page:** Interpolation Processing

**UI Elements**

* Progress bar
* Processing stage label
* Estimated time remaining
* Cancel button

**User Actions**

* Wait
* Cancel processing

**System Response**

* Frames processed sequentially
* Progress updates displayed

**Next State**

→ Export Complete Screen

**Success Criteria**

* Output video successfully generated

---

### Step 4 — Export Complete Screen

**UI Elements**

* Success message
* Open Output Folder button
* Play Output Video button
* Process Another Video button

**User Actions**

* View output
* Process another video

---

## ERROR STATES

### Invalid File Format

Condition:

User uploads unsupported file type.

System Response:

Display message:

```
Unsupported file format. Please upload MP4, AVI, or MOV files.
```

Recovery Options:

* Retry upload
* Cancel operation

---

### Corrupted Video File

Condition:

Video decoding fails.

System Response:

Display:

```
The selected file could not be processed. The file may be corrupted.
```

Recovery Options:

* Upload another file

---

### Processing Failure

Condition:

Unexpected runtime error.

System Response:

Display:

```
Processing failed. Please try again or choose a different interpolation mode.
```

Recovery Options:

* Retry
* Change mode
* Cancel operation

---

## EDGE CASES

### User Abandons Flow

User closes application during processing.

System Behavior:

* Partial output deleted
* Temporary files cleaned

---

### Session Timeout

Not applicable for offline desktop application.

---

# FLOW 2 — Preview Interpolation

---

## HAPPY PATH

### Step 1 — User Selects Preview

Location:

Processing Setup Screen

**User Action**

Click Preview

---

### Step 2 — Preview Generation

System Response:

* Extract 7 second segment
* Run interpolation

**UI**

Preview Loading Indicator

---

### Step 3 — Preview Display

**UI Elements**

* Original vs Interpolated playback
* Play/Pause controls
* Mode toggle

**User Actions**

* Compare outputs
* Return to settings

---

## ERROR STATES

Preview generation fails.

Display:

```
Preview could not be generated. Please try again.
```

---

## EDGE CASES

Preview requested for very large videos.

System Response:

Preview limited to first **7 seconds**.

---

# FLOW 3 — Account / Settings Management

Flux does **not require user accounts**, but settings exist.

---

## HAPPY PATH

### Step 1 — Open Settings

User clicks Settings icon.

**Screen:** Settings Panel

---

### Step 2 — Adjust Preferences

Options:

* Default interpolation mode
* Default target FPS
* Output directory

---

### Step 3 — Save Settings

System saves preferences locally.

---

## ERROR STATES

Invalid directory path.

Display:

```
Selected folder cannot be accessed.
```

---

# FLOW 4 — Error Recovery

---

## Scenario: Processing Interrupted

Condition:

Application crash or forced close.

---

### Step 1 — Application Restart

System scans temporary directory.

---

### Step 2 — Recovery Prompt

Display:

```
Previous processing session was interrupted.
Resume or discard partial output?
```

Options:

* Resume
* Discard

---

# 3. NAVIGATION MAP

```
Flux Application

Home / Upload Screen
│
├── Processing Setup
│   │
│   ├── Preview Screen
│   │
│   └── Processing Screen
│       │
│       └── Export Complete Screen
│
└── Settings
```

Cross-links:

* Export Complete → Upload Screen
* Preview Screen → Processing Setup

Authentication:

* None required

---

# 4. SCREEN INVENTORY

---

## Screen: Home / Upload

**Route**

```
/home
```

**Access Level**

Public

**Purpose**

Import video for processing.

**Key UI Elements**

* Upload button
* Drag-drop area
* Format info

**Actions**

Upload → Processing Setup

---

## Screen: Processing Setup

**Route**

```
/setup
```

**Purpose**

Configure interpolation settings.

**Key Elements**

* Mode selector
* Target FPS selector
* Preview button
* Start processing

---

## Screen: Processing

**Route**

```
/processing
```

**Purpose**

Display processing progress.

**States**

* Loading
* Processing
* Error

---

## Screen: Export Complete

**Route**

```
/export-complete
```

**Purpose**

Show success and output access.

---

## Screen: Settings

**Route**

```
/settings
```

**Purpose**

Configure default preferences.

---

# 5. DECISION POINTS

```
IF uploaded_file_format NOT IN [MP4, AVI, MOV]
THEN show_error("Unsupported file format")

ELSE IF file_size > 2GB
THEN show_error("File exceeds size limit")

ELSE load_video_metadata
```

---

```
IF target_fps <= input_fps
THEN show_error("Target FPS must be greater than input FPS")

ELSE enable_processing_button
```

---

```
IF processing_error
THEN show_error("Processing failed")

ELSE show_export_screen
```

---

# 6. ERROR HANDLING

---

### 404 Error

Not applicable to desktop application.

---

### 500 Error

Internal processing failure.

Display:

```
Unexpected error occurred during processing.
```

Actions:

* Retry
* Exit

---

### Network Offline

Not applicable.

Flux operates locally.

---

### Permission Denied

Condition:

Output folder restricted.

Display:

```
Flux cannot write to the selected folder.
Choose a different output location.
```

---

### Form Validation Failure

Example:

Invalid FPS selection.

Display:

```
Invalid frame rate selected.
```

---

# 7. RESPONSIVE BEHAVIOR

---

## Desktop

Primary interface.

* Full feature set
* Video preview enabled
* Detailed controls

---

## Tablet

Simplified UI layout.

Changes:

* Reduced preview size
* Larger touch controls

---

## Mobile

Not supported in initial release.

---

# 8. ANIMATIONS & TRANSITIONS

---

### Page Transitions

Duration:

```
200–300ms
```

Animation:

```
Fade + slight slide
```

---

### Processing Animation

* Animated progress bar
* Frame counter indicator

---

### Preview Loading

Loading spinner shown during preview generation.

---

### Success Animation

Small checkmark animation displayed after successful export.

---

**End of Document — Flux Application Flow**
