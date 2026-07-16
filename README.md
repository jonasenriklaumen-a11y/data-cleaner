# CleanSweep 🧹

Eine Windows-Desktop-App (WPF, .NET 8), die beim Start automatisch nach **Dateien und Anwendungen sucht, die du lange nicht mehr genutzt hast** – und dir hilft, sicher aufzuräumen.

## Features

- **Automatischer Scan beim Start** der App
- Zeitraum wählbar: **3 Monate, 6 Monate, 12 Monate oder 2 Jahre** – gelistet wird nur, was seit diesem Zeitraum weder geöffnet noch geändert wurde
- Durchsucht deine Benutzerordner: Downloads, Dokumente, Bilder, Musik, Videos, Desktop
- Erkennt **installierte Anwendungen**, deren Programmdateien lange nicht angefasst wurden
- **Übersicht vor dem Löschen**: Du siehst genau, was entfernt wird, kannst einzelne Dateien/Anwendungen abwählen oder **alles abbrechen**
- **Sicherheit zuerst:**
  - Dateien werden **in den Papierkorb verschoben**, nicht endgültig gelöscht
  - Anwendungen werden nicht einfach gelöscht – es wird das **offizielle Deinstallationsprogramm** des Herstellers gestartet (dort kannst du ebenfalls noch abbrechen)
  - Versteckte und Systemdateien werden nie angetastet

## Screenshots-Ablauf

1. App öffnen → Scan startet automatisch (Standard: 6 Monate)
2. Zeitraum oben umstellen → es wird automatisch neu gescannt
3. Haken setzen/entfernen, dann **„Bereinigen …“**
4. Übersichtsfenster prüfen → **„Jetzt bereinigen“** oder **„Alles abbrechen“**

## Installation & Build

Voraussetzung: [.NET 8 SDK](https://dotnet.microsoft.com/download/dotnet/8.0) (nur zum Bauen; zum Ausführen reicht die .NET 8 Desktop Runtime).

```powershell
git clone https://github.com/<dein-benutzername>/CleanSweep.git
cd CleanSweep
dotnet run --project src/CleanSweep
```

Release-Build als einzelne EXE:

```powershell
dotnet publish src/CleanSweep -c Release -r win-x64 --self-contained false -p:PublishSingleFile=true
```

Die EXE liegt danach unter `src/CleanSweep/bin/Release/net8.0-windows/win-x64/publish/CleanSweep.exe`.

## Wie wird „zuletzt genutzt“ bestimmt?

Für jede Datei wird das **Maximum aus letztem Lesezugriff und letzter Änderung** verwendet. Bei Anwendungen wird der jüngste Zugriff auf die `.exe`-Dateien im Installationsordner herangezogen.

> **Hinweis:** Windows aktualisiert den „letzten Zugriff“ (Last Access Time) auf NTFS nicht in jedem Fall zuverlässig – je nach Systemeinstellung kann er veraltet sein. CleanSweep verhält sich deshalb konservativ (im Zweifel gilt eine Datei als „kürzlich genutzt“) und löscht nie endgültig, sondern nur in den Papierkorb. Prüfe die Übersicht trotzdem immer sorgfältig.

## Projektstruktur

```
CleanSweep/
├── src/CleanSweep/
│   ├── Models/CleanupItem.cs      # Datenmodell für Scan-Ergebnisse
│   ├── Services/FileScanner.cs    # Scannt Benutzerordner nach alten Dateien
│   ├── Services/AppScanner.cs     # Findet lange ungenutzte Anwendungen (Registry)
│   ├── Services/RecycleBin.cs     # Verschieben in den Papierkorb (Shell-API)
│   ├── Services/Uninstaller.cs    # Startet Hersteller-Deinstallation
│   ├── MainWindow.xaml(.cs)       # Hauptfenster: Scan, Zeitraum, Auswahl
│   └── ReviewWindow.xaml(.cs)     # Übersicht mit letzter Bestätigung/Abbruch
├── .github/workflows/build.yml    # CI-Build bei jedem Push
├── CleanSweep.sln
├── LICENSE
└── README.md
```

## Lizenz

MIT – siehe [LICENSE](LICENSE).

## Haftungsausschluss

Die App ist bewusst vorsichtig gebaut, trotzdem gilt: Prüfe die Übersicht vor dem Bereinigen. Die Nutzung erfolgt auf eigene Verantwortung.
