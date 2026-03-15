# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**cmx.js** is a JavaScript library for authoring xkcd-style comics. It provides an interactive browser-based editor and SVG rendering engine using D3.js, with a hand-drawn aesthetic. Live demo: http://cmx.io

## Development Commands

```bash
npm install          # Install Node dependencies
bower install        # Install front-end dependencies (into app/components/)
grunt server         # Start dev server with livereload at http://localhost:9000
grunt build          # Full production build (output to dist/)
grunt test           # Run Mocha tests
```

For deployment (requires Ruby, Rake, and `../cmx.io/` repo checked out):
```bash
rake build           # Compile + deploy to cmx.io
```

## Architecture

The source is written in **CoffeeScript** and compiled via Grunt. AMD modules are loaded with **RequireJS**. Styles use **SCSS/Compass**. The editor uses **Ace** for code editing and **D3.js** for SVG rendering.

### Key Directories

- `app/lib/cmx/` — Core library (the main deliverable)
- `app/edit/` — The web-based comic editor UI
- `app/scripts/` — Homepage bootstrap scripts
- `test/` — Mocha/Chai browser-based tests

### Core Library (`app/lib/cmx/`)

MVC architecture with D3-based rendering:

- **`cmx.coffee`** — Entry point; creates a controller and mixes in all class constructors
- **`controller.coffee`** — Manages scenes, selection state, undo/redo
- **`scene.coffee`** — Extends Drawable; renders comic panels using D3 SVG with 3 layers
- **`entity.coffee`** — Base for all entities; handles transforms, skeleton attachment, gizmo building
- **`parser.coffee`** — Parses comic HTML markup into model hierarchy
- **`renderer.coffee`** — Low-level D3 SVG primitives (circles, lines, paths, text)
- **`model.coffee`** — Base model with typed property management and pose serialization
- **`skelet.coffee` / `bone.coffee`** — Bone-based skeletal animation system

### Entity Types (`app/lib/cmx/entities/`)

- **`actor.coffee`** — Stick figure with 14 named bones (HEAD, NECK, BDC1, BDC2, HIPS, LLEG, LFOT, RLEG, RFOT, LARM, LHND, RARM, RHND, HNDL)
- **`bubble.coffee`** — Speech/thought bubbles
- **`drawing.coffee`** — Freehand Bezier paths
- **`label.coffee`** — Text labels

Each entity has a corresponding model in `app/lib/cmx/models/` and a gizmo (interactive edit control) in `app/lib/cmx/gizmos/`.

### Editor (`app/edit/scripts/`)

- **`main.coffee`** — RequireJS config and AMD bootstrap
- **`editor.coffee`** — Integrates Ace editor with live CMX preview
- **`d3ext.coffee`** — Custom D3 extensions used by the editor

### Build Pipeline

Grunt compiles CoffeeScript → `.tmp/`, SCSS via Compass → `.tmp/`, then RequireJS bundles AMD modules for the `dist/` output. The Rakefile handles the final deployment step (copying `dist/` into the sibling `cmx.io/` repo and updating CDN references).

## Conventions

- Source files are CoffeeScript (`.coffee`); never edit generated JS directly
- AMD (`define`/`require`) module pattern throughout
- D3 selection chaining is the primary rendering approach — avoid direct DOM manipulation
- Indentation: 4 spaces (enforced by `.editorconfig`)
- JavaScript output must pass `.jshintrc` rules (camelCase, strict mode, `===`, no unused vars)
