
name: Build Android APK

on:
  push:
    branches: [ main ]
    paths:
      - 'project.godot'
      - 'scenes/**'
      - 'scripts/**'
      - 'export_presets.cfg'
      - '.github/workflows/build-android.yml'
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    container:
      image: barichello/godot-ci:4.4.1

    steps:
      - name: Checkout source
        uses: actions/checkout@v4

      - name: Check project files
        run: |
          test -f project.godot
          test -f scenes/main.tscn
          test -f scripts/main.gd
          test -f export_presets.cfg

      - name: Import project resources
        run: godot --headless --editor --path . --quit

      - name: Export Android APK
        run: |
          mkdir -p build
          godot --headless --path . --export-debug "Android" build/CityDrivingSimulator-debug.apk

      - name: Upload APK artifact
        uses: actions/upload-artifact@v4
        with:
          name: CityDrivingSimulator-Android-APK
          path: build/CityDrivingSimulator-debug.apk
          if-no-files-found: error
          retention-days: 14
          
