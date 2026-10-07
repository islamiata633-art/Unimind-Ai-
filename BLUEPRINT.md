# UniMind AI — MVP Blueprint

## MVP scope
Auth · Onboarding · AI Tutor · Courses · GPA/CGPA · Flashcards · Study Planner · File Analysis

## Folder structure
```
lib/
  main.dart
  core/            theme.dart, router.dart, providers.dart, ai_client.dart
  features/
    auth/          login, register, phone OTP, reset
    onboarding/    university > faculty > dept > level/semester > courses
    dashboard/     today plan, deadlines, streak, GPA, recs
    tutor/         chat UI, history, voice (stt/tts)
    courses/       course CRUD, assignments, tests
    planner/       day/week/month views, AI plan generation
    flashcards/    decks, cards, review (SM-2), AI generation
    gpa/           calculator, CGPA, trends
    files/         upload, status, summary, derived quizzes/cards
  shared/widgets/
functions/src/     index.ts (aiChat, generateFlashcards, generateStudyPlan, analyzeFile)
firestore.rules
```
Each feature: `data/` (repos) · `domain/` (models) · `presentation/` (screens, controllers).

## Firestore schema
- users/{uid}: role, displayName, email, plan(free|premium), createdAt
- students/{uid}: university, faculty, department, level, semester, streak, lastActiveDate
- courses/{id}: ownerId, title, code, credits, lecturer, semester, notes
  - assignments/{id}: title, dueAt, status | tests/{id}: title, date, type(test|exam)
- flashcardDecks/{id}: ownerId, courseId?, title | cards/{id}: front, back, ease, interval, reps, dueAt
- studyPlans/{id}: ownerId, range(day|week|month), startsAt | sessions/{id}: courseId, startsAt, minutes, done
- chats/{id}: ownerId, title, updatedAt | messages/{id}: role, text, createdAt
- files/{id}: ownerId, storagePath, status(uploaded|processing|ready|failed), summary, keyConcepts[] | chunks/{id}: index, text
- gpaRecords/{id}: ownerId, semester, courses[{code,credits,grade}], gpa
- usage/{uid_YYYYMMDD}: aiCalls (rate limiting)

Indexes: cards (deckId, dueAt), assignments (dueAt), sessions (startsAt).

## UI flows
1. Launch → Auth (Google / email / phone) → first login? Onboarding : Dashboard
2. Onboarding: University → Faculty → Department → Level & Semester → Add courses → generate first study plan → Dashboard
3. Tutor: Tutor tab → new/past chat → type or hold-mic → streamed answer → optional TTS → "Make flashcards from this"
4. Courses: Study tab → Courses → add/edit course → assignments/tests → deadlines feed Dashboard + Planner
5. Flashcards: Deck → add manual or "Generate with AI" (notes / selected text / file) → Review session (Again/Hard/Good/Easy) → progress
6. Planner: pick range → "Generate plan" (uses courses, deadlines, weak courses) → drag to adjust → mark sessions done
7. GPA: pick semester → add courses (credits + grade) → GPA/CGPA + trend chart → grade prediction
8. Files: Upload → processing → summary, key concepts → "Make flashcards" / "Ask tutor about this"

## Roadmap (8 weeks)
- W1: Flutter + Firebase setup, theme, routing, auth, rules, CI
- W2: Onboarding, student profile, dashboard shell
- W3: Course manager, assignments, tests
- W4: GPA/CGPA (offline-first logic, charts)
- W5: AI gateway functions, tutor chat + history, rate limits
- W6: Flashcards + SM-2 + AI generation
- W7: Study planner (AI + manual), notifications
- W8: File analysis pipeline, QA, beta release (Play Store internal, TestFlight, web on EdgeOne)

## Before launch
Firebase App Check · deploy rules · set ANTHROPIC_API_KEY secret · privacy policy · crash reporting · load test AI functions
