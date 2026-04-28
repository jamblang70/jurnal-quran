# Requirements Document

## Introduction

This document specifies requirements for enhancing the dzikir tab in the Jurnal Quran PWA application. The enhancements include four new features: auto-next mode for automatic progression between dzikir items, a new post-obligatory prayer dzikir session, daily streak tracking per session, and improved Arabic typography. These features aim to improve user experience during dzikir practice and encourage consistent daily engagement.

## Glossary

- **Dzikir_Tab**: The application tab containing dzikir sessions and items
- **Dzikir_Session**: A collection of dzikir items grouped by time (e.g., Pagi, Petang, Sholat Fardhu)
- **Dzikir_Item**: An individual dzikir with Arabic text, transliteration, target count, and current count
- **Counter_System**: The mechanism that tracks and increments dzikir recitation counts
- **Auto_Next_Mode**: A feature that automatically advances to the next dzikir item when the current item is completed
- **Streak_Tracker**: A system that tracks consecutive days of completing dzikir sessions
- **Selanjutnya_Button**: The "Next" button that appears when a dzikir item is completed
- **localStorage**: Browser storage mechanism for persisting application data
- **Service_Worker**: PWA component that manages caching (sw.js file)
- **CACHE_NAME**: Version identifier in Service_Worker that must be incremented for deployments
- **Sahih_Hadith**: Authentic hadith from Bukhari or Muslim collections
- **Arabic_Font**: The typeface used to display Arabic text in dzikir items

## Requirements

### Requirement 1: Auto-Next Mode Toggle

**User Story:** As a user performing dzikir, I want an auto-next mode that automatically advances to the next item after completion, so that I can maintain focus without manually tapping the next button.

#### Acceptance Criteria

1. THE Dzikir_Tab SHALL display a toggle switch for enabling or disabling Auto_Next_Mode
2. WHEN Auto_Next_Mode is enabled AND a Dzikir_Item reaches its target count, THE Counter_System SHALL automatically select the next Dzikir_Item within 500 milliseconds
3. WHEN Auto_Next_Mode is disabled AND a Dzikir_Item reaches its target count, THE Dzikir_Tab SHALL display the Selanjutnya_Button
4. THE Counter_System SHALL persist the Auto_Next_Mode state in localStorage with key "q-dzikir-auto-next"
5. WHEN the last Dzikir_Item in a Dzikir_Session is completed AND Auto_Next_Mode is enabled, THE Counter_System SHALL remain on the last item without advancing
6. THE toggle switch SHALL be positioned prominently in the Dzikir_Tab interface above the session selector
7. THE toggle switch SHALL use Indonesian label text "Mode Auto-Next"

### Requirement 2: Post-Obligatory Prayer Dzikir Session

**User Story:** As a Muslim user, I want a dzikir session for post-obligatory prayers based on sahih hadith, so that I can perform authentic dzikir after each of the five daily prayers.

#### Acceptance Criteria

1. THE Dzikir_Tab SHALL include a new Dzikir_Session with id "sholat-fardhu" and label "Sholat Fardhu"
2. THE Sholat_Fardhu_Session SHALL contain Dzikir_Item entries sourced exclusively from Sahih_Hadith collections (Bukhari or Muslim)
3. WHEN a user selects the Sholat_Fardhu_Session, THE Dzikir_Tab SHALL display the session title "Dzikir Setelah Sholat Fardhu"
4. THE Sholat_Fardhu_Session SHALL include the time descriptor "Setelah Sholat 5 Waktu"
5. THE Sholat_Fardhu_Session SHALL use the gradient color scheme "from-emerald-500 via-teal-600 to-cyan-700"
6. THE Sholat_Fardhu_Session SHALL use the icon "🕌"
7. EACH Dzikir_Item in the Sholat_Fardhu_Session SHALL include arabic text, latin transliteration, hadith reference note, and target count
8. THE Sholat_Fardhu_Session SHALL include at minimum: Tasbih (33x), Tahmid (33x), Takbir (34x), and Ayat Kursi (1x)
9. THE Counter_System SHALL persist Sholat_Fardhu_Session counts in localStorage under the "sholat-fardhu" key within the counts object
10. THE Dzikir_Tab SHALL display the Sholat_Fardhu_Session option in the session selector alongside Pagi and Petang sessions

### Requirement 3: Daily Streak Tracking

**User Story:** As a user, I want to track my daily streak for completing each dzikir session, so that I can stay motivated and build consistent dzikir habits.

#### Acceptance Criteria

1. THE Streak_Tracker SHALL maintain a separate streak count for each Dzikir_Session (Pagi, Petang, Sholat_Fardhu)
2. WHEN all Dzikir_Item entries in a Dzikir_Session reach their target counts on a given day, THE Streak_Tracker SHALL increment that session's streak by 1
3. WHEN a user completes a Dzikir_Session on consecutive days, THE Streak_Tracker SHALL increment the streak count
4. WHEN a user fails to complete a Dzikir_Session for one or more days, THE Streak_Tracker SHALL reset that session's streak to 0
5. THE Streak_Tracker SHALL persist streak data in localStorage with key "q-dzikir-streaks"
6. THE Dzikir_Tab SHALL display the current streak count for the active Dzikir_Session
7. THE streak display SHALL show the streak number followed by the fire emoji "🔥"
8. THE streak display SHALL be positioned near the session title or progress indicator
9. THE Streak_Tracker SHALL store the last completion date for each Dzikir_Session to detect missed days
10. WHEN the date changes AND the previous day's session was not completed, THE Streak_Tracker SHALL reset the streak to 0 before the user's first interaction

### Requirement 4: Enhanced Arabic Typography

**User Story:** As a user reading Arabic dzikir text, I want a more beautiful and readable Arabic font, so that I can read the text comfortably and appreciate its aesthetic quality.

#### Acceptance Criteria

1. THE Dzikir_Tab SHALL use an Arabic_Font from Google Fonts (Amiri, Scheherazade New, or Noto Naskh Arabic)
2. THE application SHALL load the Arabic_Font via a link tag in index.html
3. THE Arabic_Font SHALL be applied to all arabic text fields in Dzikir_Item entries
4. THE Arabic_Font SHALL have a font size of at least 24 pixels for readability
5. THE Arabic_Font SHALL support right-to-left text rendering
6. WHEN the Arabic_Font fails to load, THE Dzikir_Tab SHALL fall back to the system Arabic font
7. THE Service_Worker SHALL include the Google Fonts CSS URL in the EXTERNAL_CACHE array
8. THE Service_Worker CACHE_NAME SHALL be incremented after implementing the Arabic_Font changes

### Requirement 5: Deployment and Cache Management

**User Story:** As a developer, I want proper cache versioning after each feature deployment, so that users receive the latest version without stale cached files.

#### Acceptance Criteria

1. WHEN any feature in this specification is implemented, THE Service_Worker CACHE_NAME SHALL be incremented
2. THE build process SHALL execute "npm run build" to compile app.jsx to app.js
3. THE Service_Worker SHALL cache the new Arabic_Font CSS file from Google Fonts
4. THE application SHALL maintain backward compatibility with existing localStorage data structures
5. WHEN localStorage data from a previous version is detected, THE application SHALL migrate or preserve the data without loss

### Requirement 6: Data Persistence and Structure

**User Story:** As a user, I want my dzikir progress, settings, and streaks to be saved automatically, so that I don't lose my data when I close the app.

#### Acceptance Criteria

1. THE Counter_System SHALL persist all dzikir counts in localStorage with key "q-dzikir-counts"
2. THE counts data structure SHALL include a date field and session-specific count objects (pagi, petang, sholat-fardhu)
3. WHEN the date changes, THE Counter_System SHALL reset all counts to empty objects while preserving the date field
4. THE application SHALL persist the active Dzikir_Session selection in localStorage with key "q-dzikir-active"
5. THE application SHALL persist the selected Dzikir_Item id in localStorage with key "q-dzikir-selected"
6. THE Streak_Tracker SHALL store streak data as an object with session ids as keys and objects containing streak count and last completion date as values
7. THE application SHALL validate localStorage data on load and handle corrupted or missing data gracefully by initializing default values

