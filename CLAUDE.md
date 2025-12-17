# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is an experimental repository for testing and learning about coding agent tools (Cline, Claude Code). It serves as a sandbox environment to explore agent features, permissions, security models, subagents, and skills.

## Development Environment

This repository uses VS Code Dev Containers for a consistent development environment:

- Base image: Ubuntu 24.04 LTS (noble)
- Timezone: Asia/Tokyo
- Available tools: Git, Tmux

To rebuild the dev container after configuration changes:
```bash
# Rebuild the container from VS Code command palette:
# "Dev Containers: Rebuild Container"
```

## Current State

This repository is in early setup phase with no production code yet. The main focus is on:

1. Learning coding agent permissions and security models
2. Exploring subagent functionality
3. Understanding agent skills framework

## Git Workflow

Main branch: `main`

Standard git operations:
```bash
# Check status
git status

# Stage and commit changes
git add <files>
git commit -m "message"

# Push changes
git push origin main
```

## Future Development

As this repository evolves to include actual projects, update this file with:
- Build commands for any frameworks or languages added
- Test commands and test running instructions
- Project-specific architecture and component organization
- Integration points between different tools or systems
