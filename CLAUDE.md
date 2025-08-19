# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

DynaBIMToolbox is a C# library providing Zero Touch nodes for Dynamo, specifically designed for BIM automation with Autodesk Revit. The library focuses on high-performance Boolean and Solid operations directly through the Revit API, aimed at improving performance for time-consuming tasks.

**Version**: 1.0.6
**Target Framework**: .NET Framework 4.8
**Primary Dependencies**: 
- DynamoVisualProgramming.ZeroTouchLibrary (2.18.1.5096)
- Autodesk Revit 2021 API (RevitAPI.dll, RevitAPIUI.dll)
- RevitNodes and RevitServices

## Development Commands

### Build Commands
```bash
# Build in Debug mode
dotnet build --configuration Debug

# Build in Release mode  
dotnet build --configuration Release

# Build for x64 platform specifically
dotnet build --configuration Debug --arch x64
```

### Project Structure
- **Main Library**: `BooleanAPI.cs` - Contains all public API classes
- **Helper Classes** (in `Invisible/` folder):
  - `SolidConversions.cs` - Solid geometry operations and transformations
  - `SurfaceConversions.cs` - Surface/face analysis utilities
  - `PointConversions.cs` - Point transformation utilities  
  - `CurveConvertions.cs` - Curve transformation utilities

## Architecture

### Core Namespaces
1. **GeometryAPI** - Main geometry operations namespace containing:
   - `SolidsAPI` - Solid generation, Boolean operations, transformations
   - `SurfacesAPI` - Surface/face operations and room boundary analysis
   - `BooleanAPI` - Boolean intersection/union operations optimized for performance

2. **Inspect** - Data extraction and analysis namespace containing:
   - `ElementsData` - Element properties, parameters, family/type information
   - `Lists` - List manipulation utilities (clean, combine, search)
   - `DocumentData` - Document-level data (phases, project information)
   - `GeometryData` - Geometric property extraction

3. **Generate** - Geometry creation namespace containing:
   - `Geometry` - Basic geometry creation utilities

### Key Technical Features

#### Coordinate System Handling
- All measurements are converted between Dynamo (cm) and Revit API (feet) using factor 30.48
- Extensive support for linked model coordinate transformations via `RevitLinkInstance`
- Transform operations handle translation, rotation, and scaling between coordinate systems

#### Performance Optimizations
- Direct Revit API operations bypass Dynamo geometry conversion overhead
- Bounding box intersection checks before expensive Boolean operations
- Solid union operations with fallback handling for failed Boolean operations
- Room boundary calculations optimized for large datasets

#### Hidden Helper Classes
All classes in the `Invisible/` namespace are marked with `[IsVisibleInDynamoLibrary(false)]` to keep internal utilities hidden from Dynamo users while providing essential functionality:
- Geometry extraction from Revit elements
- Coordinate transformations for linked models
- Surface analysis (vertical/horizontal detection, area calculations)
- Point/curve transformation utilities

### Important Development Notes

#### Error Handling
- All public methods include try-catch blocks that re-throw exceptions
- Boolean operations include fallback mechanisms for failed operations
- Null checking and validation for input parameters

#### API Compatibility
- Designed for Revit 2021 API (paths hardcoded to `C:\Program Files\Autodesk\Revit 2021\`)
- Uses older C# language features (version 8.0) for compatibility
- Extensive use of Revit API geometry types (`Autodesk.Revit.DB.Solid`, `PlanarFace`, etc.)

#### Documentation Format
- XML documentation follows Dynamo node convention with `<param>`, `<returns>`, and `<search>` tags
- Multi-return nodes use `[MultiReturn]` attribute with dictionary return types
- Search tags help users find nodes in Dynamo library browser