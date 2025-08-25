# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is **react-native-filament**, a React Native library providing 3D rendering capabilities using Google's Filament engine and Bullet3 physics. The project structure follows a monorepo pattern with the main package in `/package` and example applications in `/package/example`.

## Essential Development Commands

### Setup and Build Commands
```bash
# Initial setup (run from package directory)
cd package
yarn                                    # Install dependencies
git submodule update --init --recursive --depth 1  # Initialize submodules
yarn build-filament:release            # Build Filament engine (release)
yarn build-filament:debug              # Build Filament engine (debug) 
yarn build-bullet3                     # Build Bullet3 physics engine
yarn build                             # Build the React Native package
```

### Development Workflow Commands
```bash
# Code quality checks
yarn check-all                         # Run all checks (format C++, lint JS/TS, typecheck)
yarn lint                              # ESLint for JavaScript/TypeScript
yarn typescript                        # TypeScript type checking
./scripts/clang-format.sh              # Format C++ code

# Testing and Development
yarn codegen                           # Generate React Native code
```

### Building and Running Examples
```bash
# Navigate to example apps
cd package/example/AppExamplePaper      # Paper architecture example
cd package/example/AppExampleFabric     # Fabric architecture example

# Example apps use standard React Native commands:
npm run android
npm run ios
```

## Architecture Overview

### Core Technology Stack
- **Frontend**: React Native with TypeScript, supporting both Paper and Fabric architectures
- **3D Rendering**: Google Filament engine (C++) with Metal (iOS) and Vulkan/OpenGL (Android)
- **Physics**: Bullet3 physics engine (C++)
- **JavaScript Bridge**: JSI (JavaScript Interface) with react-native-worklets-core for performance
- **Build System**: CMake for native code, Gradle for Android, CocoaPods for iOS

### Directory Structure
- `/package/src/` - TypeScript/React components and hooks
- `/package/cpp/` - C++ native implementation with JSI bindings
- `/package/ios/` - iOS-specific code and build configuration
- `/package/android/` - Android-specific code and build configuration
- `/filament/` - Google Filament submodule
- `/bullet3/` - Bullet3 physics submodule

### Key Components

#### React API Layer (`/package/src/react/`)
- `FilamentView` - Main 3D rendering surface component
- `FilamentScene` - Scene container component
- `Model` - 3D model component (supports GLB format)
- `Camera` - Camera configuration component
- `Light`/`DefaultLight` - Lighting components
- `Skybox` - Skybox/environment component

#### Native Implementation (`/package/cpp/`)
- **Core Engine**: `RNFEngineImpl`, `RNFFilamentProxy` - Main Filament engine integration
- **Components**: Wrappers for Filament entities (Camera, Light, Material, etc.)
- **Physics**: Bullet3 integration with rigid bodies, shapes, and collision detection
- **JSI Bridge**: `RNFHybridObject` base class for JavaScript-C++ object bridging

#### Hook System (`/package/src/hooks/`)
- Performance-critical operations using worklets (off-main-thread execution)
- Resource management hooks with automatic cleanup
- Animation hooks leveraging Reanimated integration

## Development Guidelines

### Native Code Development
- C++ code uses modern C++17 standards
- All native objects inherit from `RNFHybridObject` for JSI integration
- Memory management follows RAII principles with automatic cleanup
- Performance-critical operations run on background threads

### TypeScript Development
- Strict TypeScript configuration
- Hooks follow React patterns with proper dependency arrays
- Worklets used for performance-critical operations
- Resource cleanup handled automatically via hooks

### Build System Requirements
- **iOS**: Xcode with Metal support, minimum iOS version from `min_ios_version_supported`
- **Android**: NDK with CMake, minimum API level defined in build.gradle
- **Filament**: Must be built before package compilation
- **Bullet3**: Required for physics functionality

### Material System
- Materials use Filament's `.mat` format
- Custom materials can be compiled using `scripts/compile-materials.sh`
- Default materials provided in `/package/assets/`

## Common Development Tasks

### Adding New 3D Components
1. Create TypeScript interface in `/package/src/types/`
2. Implement C++ wrapper in `/package/cpp/core/`
3. Add JSI bindings in corresponding wrapper class
4. Create React component in `/package/src/react/`
5. Export from `/package/src/index.tsx`

### Physics Integration
- Use hooks in `/package/src/bullet/hooks/` for physics objects
- Rigid bodies, shapes, and worlds are managed through the hook system
- Physics simulation runs on dedicated thread

### Performance Optimization
- Use worklets for frame-rate dependent operations
- Leverage Reanimated shared values for smooth animations
- Minimize main thread work through proper threading

### Debugging
- Enable logging via `$RNFEnableLogs` flag in podspec
- Use Filament's built-in debugging tools
- Physics debugging available through Bullet3 debug renderer

This codebase requires careful coordination between React Native, C++ native code, and two complex 3D/physics engines. Always ensure proper resource management and thread safety when making modifications.