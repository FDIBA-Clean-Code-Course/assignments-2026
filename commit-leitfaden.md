# Commit Leitfaden

## Warum Conventional Commits?

Conventional Commits bieten eine einfache Struktur für unsere Commit-Nachrichten. Es ist wichtig, eine gute Gewohnheit aufzubauen, klare, deskriptive und gut strukturierete Commit-Nachrichten zu schreiben. Natürlich es existieren andere Formaten
und es hängt vom Team ab, die Struktur der Nachricht zu diskutieren (und auch die Bedeutung der verschiedenen Commit-Tags zu erklären).

## Struktur
Wir werden nicht alle Teile eines Conventional-Commits verwenden - unsere Commits müssen nur einen Tag und eine deskriptive Nachricht erhalten:

{tag}: nachricht

Zum Beispiel:
- feat: Created an util function for calculating distances between two objects
- fix: Fixed an issue where object distances were not calculated correctly
- docs: Updated repo documentation
- style: Added a stylesheet for the home page
- refactor: Changed calculation formula for the distance util function

Beispiele für schlechte Commits:
- fix
- fix fix
- fix fix fix
- Calc function progress made
- adjusted styles
- adjusted function

Gute Struktur und klare Commit-Nachrichten dienen dazu, eine klare Git-Geschichte in unseren Projekten zu bauen.

## Tag Bedeutung in unserem Kurs

- feat - beschreibt eine neue Funktionalität
- fix - beschreibt ein Bugfix
- chore - In meiner Praxis, verwende ich chore für Build-Fehler-Fixes, Lint-Fehler-Fixes etc. Das gilt auch in unserem Kurs.
- refactor - beschreibt eine Funktionalität die umgeschrieben wurde, um sie zu verbessern
- docs - für README- und Dokus-Updates
- style - am meisten bei der Frontend-Entwicklung - für CSS-Änderungen

Mehr über Conventional Commit - [hier](https://www.conventionalcommits.org/en/v1.0.0/)
