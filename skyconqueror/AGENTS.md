# AGENTS.md

This file provides guidance to AI agents when working with code in this repository.

## General Workflow

For general workflow rules (branching, conventional commits, draft PRs, git remote configuration, validation before committing, PR descriptions, Jira-to-PR sync), see the [top-level AGENTS.md](../AGENTS.md).

## Context for these projects

All the projects in this folder serve one goal - the promotion of the underground heavy metal band SKYCONQUEROR located in Dortmund, Germany. Main objectives for the band are a broad online reach and the discovery by new fans.

Some links:
* https://skyconqueror.de/ - this is the primary website URL for the bands homepage, also this is the target of where contents of the project skyconqueror.github.io are published
* https://skyconqueror.bandcamp.com/ - this is the primary online store URL hosted by bandcamp, where fans can buy digital music and physical merch, like CDs etc.

## Project Overview

This repository contains two main projects:

1.  **`skyconqueror.github.io`**: A Jekyll-based website for the band Skyconqueror. It serves as the band's official website, providing information about gigs, news, and contact details.
2.  **`event-qr-code-sender`**: A command-line tool written in Go. This tool is used to generate and send QR code tickets for events. It reads data from a CSV file, generates ticket images with QR codes, and sends them to recipients via Gmail.

## `skyconqueror.github.io`

### Building and Running

The website is built using Jekyll. The following commands are used for local development:

*   **Install dependencies:** `make install`
*   **Start local server:** `make serve`

### Development Conventions

*   The website is hosted on GitHub Pages.
*   The `_gigs` directory contains markdown files for each gig.
*   The `_config.yml` file contains the main configuration for the Jekyll site.

## `event-qr-code-sender`

### Building and Running

This is a Go project. The following commands are used to run the tool:

*   **Run the application:** `go run main.go run [flags]`

### Development Conventions

*   The project uses the `cobra` library for command-line parsing.
*   The main logic is in the `cmd` directory.
*   The tool uses the Gmail API to send emails. Credentials for the API are stored in a `credentials.json` file.
*   The tool reads event data from a CSV file.
