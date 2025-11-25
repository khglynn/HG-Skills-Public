---
title: Solo Web Development Workflow
description: Git and deployment workflow for solo web projects - push to main, link Vercel previews, branch only when requested.
---

# Solo Web Development Workflow

This skill defines the default workflow for web development projects.

## Core Assumptions

- All web projects are **solo endeavors** unless explicitly stated otherwise
- User will specify if/when they want to deviate from defaults

## Git Workflow

### Default: Push to Main
- Push changes directly to `main` branch
- No feature branches unless explicitly requested
- Commit with clear, descriptive messages

### When to Branch
Only create feature branches when the user explicitly requests:
- "Create a branch for this"
- "Let's work on a feature branch"
- "Don't push to main yet"

## Deployment (Vercel)

For projects with a UI:

### After Pushing
1. **Always link to the Vercel preview/production URL**
2. Show the live build preview when available
3. Note if build is still deploying

### Example Response Format
> Pushed to main. 
> 🔗 Preview: https://project-name.vercel.app
> Build status: ✅ Deployed (or ⏳ Deploying...)

## Summary

| Action | Default | Override |
|--------|---------|----------|
| Branch | Push to `main` | User requests branch |
| Deploy | Link Vercel preview | N/A |
| Review | Not required | User requests review |
