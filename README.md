# rezcad
This CAD program uses how close the camera is to an object to determine rezolution. It is derived from an app called Qubism

Qubism Reconstruction

Purpose

This project is reconstructing the old Qubism application from the available decompiled source in:

"https://github.com/ivanferrier55/rezcad"

The repository contains a JADX-decompiled version of the original application. The source is heavily obfuscated, so the objective is not simply to rename classes.

Our objective is to recover enough understanding of the original application to produce a clean, maintainable reconstruction with behavior that matches the original.

The project is organized around two principles:

1. Understand before rewriting.
2. Evidence before assumptions.

The reconstruction should ultimately allow an engineer who has never seen the obfuscated source to understand how Qubism works.

---

PART 1 — WHAT NEEDS TO BE DONE

1. Overall Reconstruction Pipeline

The project follows this pipeline:

Original Qubism
      │
      ▼
JADX Decompiled Source
      │
      ▼
Code Archaeology
      │
      ▼
Semantic Names
      │
      ▼
Domain / Architecture Reconstruction
      │
      ▼
Behavioral Specification
      │
      ▼
Tests
      │
      ▼
Clean Reimplementation
      │
      ▼
Behavioral Comparison
      │
      ▼
Reconstructed Qubism

Do not skip directly from obfuscated source to a new implementation.

We need to understand what the original application actually does first.

---

2. Primary Workstreams

The project is divided into seven major workstreams.

A. Domain Model

Goal

Understand the core objects that represent a Qubism document.

Investigate:

jquinn.qubism
jquinn.qubism.b
jquinn.qubism.c
jquinn.qubism.e

Questions to answer:

- What is a Qube?
- What is a document?
- How are Qubes identified?
- How are Qubes positioned?
- How is size represented?
- How is color represented?
- How are shapes represented?
- What is the camera state?
- What is selection state?
- What state is persistent?
- What state is transient?
- How are objects related?

Known clues

The source already suggests concepts such as:

Qube
position
size
colour
shape
camera state
selection
UUID

There are also physical units:

Meters
Feet
Inches
Centimeters
Millimeters

Deliverables

docs/domain-model.md
docs/geometry.md
docs/symbol-map-domain.csv

---

3. Action / Editing System

Goal

Reconstruct the application's editing model.

The source explicitly exposes operations including:

Multi
Add
Move
Paint
Remove
Shape
Rotate

Investigate:

- How an action is created
- How an action executes
- How an action is undone
- How an action is redone
- How multiple actions are grouped
- How actions reference Qubes
- How camera state participates in actions
- How selection participates in actions
- How actions are serialized

Deliverables

docs/action-system.md
docs/symbol-map-actions.csv

Create a table mapping obfuscated classes to semantic concepts:

Original     Proposed Name       Confidence
------------------------------------------------
a.b          AddQubeAction       HIGH
a.f          PaintQubeAction     HIGH
...

Every proposed name must include evidence.

---

4. Geometry

Goal

Determine the mathematical model behind Qubism.

Investigate:

- Coordinate system
- X/Y/Z axes
- Origin
- Units
- Integer vs floating-point coordinates
- Dimensions
- Bounding boxes
- Grid spacing
- Shape dimensions
- Coordinate conversions
- Screen → model conversion
- Model → screen conversion

Investigate particularly:

jquinn.a
jquinn.qubism.e
jquinn.qubism.g

The application appears to use three-dimensional coordinates and dimensions.

Do not assume the coordinate system until it has been validated.

Deliverables

docs/geometry.md
docs/coordinate-system.md

---

5. Rendering / Camera

Goal

Understand how the 3D-ish CAD view is generated and manipulated.

Investigate:

jquinn.qubism.g
jquinn.qubism.f
jquinn.a

Known UI concepts include:

Orbit
Focus
Zoom
Camera
Pan
Rotate
GridSize

Known rendering/shading concepts include:

VertexShading
FaceShading
DepthShading
FlatShading

Investigate:

- Camera representation
- Projection
- Zoom
- Pan
- Orbit
- Rotation
- Focus
- Grid
- Object picking
- Depth ordering
- Shading
- Line rendering
- Shape rendering
- Viewport invalidation

Deliverables

docs/rendering-model.md
docs/camera.md
docs/picking.md

---

6. Shapes

Goal

Build a definitive inventory of the geometric primitives supported by Qubism.

Known examples include:

Cube
CubeToCircle
Elbow
Axle
HalfSphere
EighthSphere
InvEighthSphere
Elbow1_2
Elbow1_3
Elbow1_4
Cone
...

Do not assume the above list is complete.

Determine:

- All supported shapes
- Shape IDs
- Shape geometry
- Shape parameters
- Rendering behavior
- Selection behavior
- Transformation behavior
- Persistence representation

Deliverables

docs/shapes.md
docs/shape-reference.md

Where possible, create visual references for each primitive.

---

7. Android Application

Goal

Understand how the original Android application connects the domain model to the UI.

Investigate:

jquinn.qubism.android

Also inspect:

R.java
layouts
drawables
menus
strings
dimensions
styles

Known UI concepts include:

Delete
Duplicate
Send
Drag
Preview
Save
Revert
Settings
Scale

Determine:

- Activities
- Fragments
- Views
- Adapters
- Dialogs
- Menus
- Input handling
- Navigation
- Lifecycle
- UI → domain interactions

Deliverables

docs/android-ui-map.md
docs/resources.md
docs/screen-flow.md

---

8. Persistence

Goal

Understand how Qubism saves and loads documents.

Investigate:

- File storage
- Serialization
- Deserialization
- SQLite
- ContentProvider
- UUID persistence
- Import/export
- Saved documents
- Preview/thumbnail storage

The Android package contains a "ContentProvider"; determine exactly what role it plays.

Deliverables

docs/persistence.md
docs/file-format.md

If a binary or structured file format exists, document it precisely.

---

9. Decompiler Forensics

Goal

Identify places where JADX has produced unreliable or misleading code.

Search for:

Method not decompiled
UnsupportedOperationException
To view this dump
synthetic
access$
valuesCustom
switch-map

For every important damaged method, document:

Class
Method
Why it matters
Callers
Available evidence
Possible reconstruction
Confidence
Required validation

Deliverables

docs/decompiler-damage.md
docs/reconstruction-evidence.md

---

10. Runtime / Behavioral Validation

Where the original application or APK can be executed, use it as the behavioral reference.

Build a test matrix covering:

Behavior| Original| Reconstruction
Create Qube| ☐| ☐
Move Qube| ☐| ☐
Paint Qube| ☐| ☐
Delete Qube| ☐| ☐
Duplicate Qube| ☐| ☐
Change shape| ☐| ☐
Rotate| ☐| ☐
Undo| ☐| ☐
Redo| ☐| ☐
Zoom| ☐| ☐
Pan| ☐| ☐
Orbit| ☐| ☐
Focus| ☐| ☐
Grid size| ☐| ☐
Units| ☐| ☐
Save| ☐| ☐
Load| ☐| ☐

Whenever possible, record:

- Screenshot
- Input
- Expected behavior
- Actual behavior
- Numeric values
- Relevant source code
- Confidence

---

11. Symbol Reconstruction

The original code uses heavily abbreviated names.

Example:

class a

does not automatically mean:

Action

A name should be reconstructed from evidence.

Create:

docs/symbol-map.csv

Use:

package
original_class
original_method
proposed_name
category
confidence
evidence
notes

Confidence:

HIGH

Directly supported by multiple pieces of evidence.

MEDIUM

Strongly implied but not conclusively established.

LOW

Plausible hypothesis requiring validation.

Only HIGH-confidence names should be treated as established facts.

---

12. What Counts as Evidence?

Use evidence in roughly this order:

1. Runtime behavior
2. Multiple independent call sites
3. Enum names
4. Resource names
5. Constructor parameters
6. Field usage
7. Serialization format
8. Interface contracts
9. Method behavior
10. Variable names
11. Decompiler-generated names

Decompiler-generated names are weak evidence.

For example:

private int a;

tells us almost nothing.

But:

this.a = UUID.randomUUID();

combined with:

map.get(uuid)

and:

remove(uuid)

may provide strong evidence that the field represents an object identifier.

---

13. Final Architecture

The final architecture should emerge from the evidence.

A likely conceptual structure is:

                 ┌────────────────────┐
                 │     Android UI     │
                 └─────────┬──────────┘
                           │
                 ┌─────────▼──────────┐
                 │ Input / Controller │
                 └─────────┬──────────┘
                           │
             ┌─────────────┼─────────────┐
             │             │             │
       ┌─────▼─────┐ ┌─────▼─────┐ ┌─────▼─────┐
       │  Actions  │ │   Camera  │ │ Selection │
       └─────┬─────┘ └─────┬─────┘ └─────┬─────┘
             │             │             │
             └─────────────┼─────────────┘
                           │
                    ┌──────▼──────┐
                    │ Domain Model│
                    └──────┬──────┘
                           │
                    ┌──────▼──────┐
                    │   Renderer  │
                    └─────────────┘

This is a working hypothesis, not a statement about the original architecture.

The workers must validate it.

---

14. Definition of Done

The reverse-engineering phase is complete when we can answer:

- What is the Qubism document model?
- What is a Qube?
- How are Qubes positioned?
- How are Qubes sized?
- What shapes exist?
- How are shapes rendered?
- How does the camera work?
- How does zoom work?
- How does pan work?
- How does orbit work?
- How does picking work?
- How does the grid work?
- What does each editing operation do?
- How does undo/redo work?
- How does multi-action grouping work?
- How are documents saved?
- How are documents loaded?
- What belongs to the UI?
- What belongs to the domain?
- Which methods were damaged by JADX?
- Which behaviors have been verified against the original?

The resulting documentation should be sufficient for a new engineer to implement Qubism without needing to understand the obfuscated source.

---

PART 2 — HOW WE ORGANIZE THE PEOPLE

15. Team Structure

We have approximately 30 contributors.

Do not put all 30 people into one general-purpose team.

Use small teams with clear ownership.

Recommended structure:

                         PROJECT LEAD
                              │
             ┌────────────────┼────────────────┐
             │                │                │
       RESEARCH LEAD     ARCHITECTURE     INTEGRATION LEAD
             │                │                │
      ┌──────┼──────┐         │         ┌──────┼──────┐
      │      │      │         │         │      │      │
   Domain  Actions Geometry  Renderer  Android Persistence
      │      │      │         │         │      │
      └──────┴──────┴─────────┴─────────┴──────┘
                         │
                    VALIDATION

The exact number of people per team can change.

The important rule is:

«Every area has one owner, even if several people contribute to it.»

---

16. Suggested 30-Person Allocation

Leadership / Coordination — 3

1 Project Lead

Owns:

- Overall direction
- Priorities
- Cross-team conflicts
- Final decisions when evidence is ambiguous
- Milestones

2 Research Lead

Owns:

- Reverse-engineering methodology
- Evidence quality
- Symbol maps
- Research standards
- Preventing unsupported assumptions

3 Integration Lead

Owns:

- Combining subsystem discoveries
- Canonical architecture
- Cross-team interfaces
- Final reconstruction specification

---

17. Domain Team — 4

Responsibilities

Qube model
Document model
IDs
Selection
Colors
Dimensions
Units
Core data structures

Suggested roles:

Domain Lead
Data Model Researcher
Geometry Researcher
Serialization Liaison

Primary output:

docs/domain-model.md
docs/geometry.md

---

18. Action Team — 4

Responsibilities

Add
Move
Paint
Remove
Shape
Rotate
Multi
Undo
Redo

Suggested roles:

Action Lead
Command Researcher
Undo/Redo Researcher
Behavior Validator

Primary output:

docs/action-system.md

---

19. Rendering / Geometry Team — 5

Responsibilities

Camera
Projection
Zoom
Pan
Orbit
Focus
Picking
Grid
Shading
Shape rendering
Coordinate transformations

Suggested roles:

Rendering Lead
Camera Researcher
Geometry Researcher
Picking/Input Researcher
Visual Validation Researcher

Primary output:

docs/rendering-model.md
docs/camera.md
docs/picking.md
docs/shapes.md

---

20. Android / UI Team — 4

Responsibilities

Activities
Views
Menus
Dialogs
Input
Navigation
Resources
Screen state

Suggested roles:

Android Lead
UI Researcher
Input Researcher
Resource Archaeologist

Primary output:

docs/android-ui-map.md
docs/resources.md
docs/screen-flow.md

---

21. Persistence Team — 3

Responsibilities

Files
Serialization
SQLite
ContentProvider
Import/export
Saved documents

Suggested roles:

Persistence Lead
Format Researcher
Storage/Android Researcher

Primary output:

docs/persistence.md
docs/file-format.md

---

22. Validation Team — 4

This team should operate across all other teams.

Responsibilities

- Run the original application
- Record behavior
- Compare screenshots
- Reproduce bugs
- Test reconstructed behavior
- Validate hypotheses
- Build regression tests

Suggested roles:

Validation Lead
Behavior Tester
Visual Tester
Regression/Test Engineer

The validation team should be treated as an independent source of evidence.

A team should not be allowed to mark its own uncertain hypothesis as confirmed without validation where validation is possible.

---

23. Decompiler / Forensics Team — 3

Responsibilities

- JADX artifacts
- Broken methods
- Synthetic classes
- Synthetic switch maps
- Control-flow reconstruction
- Decompiled-code anomalies

Suggested roles:

Forensics Lead
Decompiler Researcher
Control-Flow Researcher

Primary output:

docs/decompiler-damage.md
docs/reconstruction-evidence.md

---

24. Contributor Assignment

Every contributor should have:

Team
Primary responsibility
Secondary responsibility
Current task
Deliverable
Status

Example:

Name: Alice
Team: Action
Primary: Undo/Redo
Secondary: Action serialization
Task: Reconstruct MultiAction
Deliverable: docs/action-system.md
Status: IN PROGRESS

Avoid assignments such as:

«"Look through the code and figure stuff out."»

Every task should produce an artifact.

---

25. Task Format

All work should be tracked using this structure:

TASK:
Reconstruct PaintAction

OWNER:
Contributor name

AREA:
Action System

SOURCE:
jquinn/qubism/a/f.java

QUESTION:
What state does PaintAction modify?

EVIDENCE:
- Constructor fields
- execute()
- undo()
- callers
- serialization

EXPECTED OUTPUT:
docs/action-system.md

CONFIDENCE:
Unknown

STATUS:
IN PROGRESS

---

26. Status Definitions

Use only these statuses:

UNASSIGNED
QUEUED
IN PROGRESS
BLOCKED
NEEDS REVIEW
VALIDATED
COMPLETE

IN PROGRESS

Someone is actively investigating it.

BLOCKED

The worker needs something from another team.

NEEDS REVIEW

The worker has produced an interpretation but another person needs to verify it.

VALIDATED

Evidence supports the interpretation.

COMPLETE

The required artifact has been integrated into the canonical documentation.

---

27. Pull Request Rules

Every PR should answer:

What did you investigate?

What did you discover?

What evidence supports it?

What remains uncertain?

What files/documentation changed?

Does another team need to review this?

Avoid giant PRs.

Prefer:

One subsystem
One question
One reconstruction
One test

over:

Rename 200 classes

---

28. Evidence Rules

When documenting a conclusion, use:

Claim:
PaintAction modifies the Qube color.

Evidence:
1. Enum j.Paint
2. Class a.f
3. Constructor stores integer color/state
4. execute() modifies the Qube model
5. undo() restores prior state
6. Call sites invoke it from color-editing UI

Confidence:
HIGH

Do not write:

I think this is probably the color class.

without recording why.

---

29. Cross-Team Dependencies

The major dependency chain is:

Geometry
    ↓
Domain Model
    ↓
Actions
    ↓
Rendering
    ↓
Android UI
    ↓
Integration

But these teams can work in parallel during research.

For example:

Domain ──────────────┐
                     │
Actions ─────────────┤
                     ├──► Architecture
Geometry ────────────┤
                     │
Rendering ───────────┤
                     │
Android ─────────────┘

Do not block an entire team waiting for another team's final answer.

Record assumptions explicitly and continue.

---

30. Weekly Project Rhythm

Use a simple cycle.

Start of cycle

Each team reports:

Completed
Current task
Blocked by
Next deliverable
Unknowns

During cycle

Contributors work independently within their assigned areas.

Important discoveries should be added to the shared documentation immediately rather than remaining in private notes.

End of cycle

Each team produces:

Discoveries
Evidence
Open questions
Validated conclusions
New tasks

The Integration Lead then updates the canonical architecture.

---

31. Shared Repository Structure

Use:

/
├── README.md
│
├── docs/
│   ├── architecture.md
│   ├── domain-model.md
│   ├── geometry.md
│   ├── action-system.md
│   ├── rendering-model.md
│   ├── camera.md
│   ├── picking.md
│   ├── shapes.md
│   ├── android-ui-map.md
│   ├── resources.md
│   ├── screen-flow.md
│   ├── persistence.md
│   ├── file-format.md
│   ├── decompiler-damage.md
│   └── reconstruction-evidence.md
│
├── mappings/
│   ├── symbols.csv
│   ├── domain.csv
│   ├── actions.csv
│   └── rendering.csv
│
├── research/
│   ├── screenshots/
│   ├── runtime/
│   ├── experiments/
│   └── notes/
│
├── tests/
│
└── reconstruction/

---

32. The Most Important Rule

The project should optimize for shared understanding, not individual productivity.

If one contributor figures something out but nobody else can understand how they reached the conclusion, the discovery is not finished.

Every significant discovery should therefore become:

Evidence
   ↓
Documentation
   ↓
Review
   ↓
Validation
   ↓
Canonical knowledge

---

33. Final Goal

At the end of this project we should have three things:

1. Historical understanding

A documented explanation of how Qubism worked.

2. Technical specification

A clean description of:

Domain
Geometry
Rendering
Camera
Actions
Persistence
Android UI

3. Clean reconstruction

A maintainable implementation that reproduces the important behavior of the original application.

The obfuscated source is the evidence.

It is not the architecture.

The architecture is what we reconstruct from the evidence.