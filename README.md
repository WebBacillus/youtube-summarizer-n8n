# YouTube Summarizer

![The n8n workflow that turns saved videos into Obsidian notes](assets/workflow.png)

**Keep the ideas. Skip the clickbait.**

<p align="left">
  <img src="https://img.shields.io/badge/n8n-automation-EA4B71?logo=n8n&logoColor=white" alt="n8n" />
  <img src="https://img.shields.io/badge/Apify-transcripts-00AEEF?logo=apify&logoColor=white" alt="Apify" />
  <img src="https://img.shields.io/badge/OpenAI-summaries-412991?logo=openai&logoColor=white" alt="OpenAI" />
  <img src="https://img.shields.io/badge/Notion-input-000000?logo=notion&logoColor=white" alt="Notion" />
  <img src="https://img.shields.io/badge/Dropbox-delivery-0061FF?logo=dropbox&logoColor=white" alt="Dropbox" />
  <img src="https://img.shields.io/badge/Obsidian-notes-7C3AED?logo=obsidian&logoColor=white" alt="Obsidian" />
</p>

## What it is

A local n8n workflow that watches a Notion database for saved YouTube videos, retrieves timestamped transcripts through Apify, and uses an OpenAI model to prepare structured Markdown notes. Dropbox syncs the notes into an Obsidian vault, where inline wiki links connect ideas across videos.

## Why I built it

I often find useful ideas in videos wrapped in clickbait framing. I wanted the substance without spending time on delivery designed to provoke a reaction. This workflow gives me a readable breakdown that I can revisit and build on in Obsidian.

## How it works

```mermaid
sequenceDiagram
    actor Me
    participant Notion as Notion database
    participant n8n as Local n8n
    participant Apify
    participant OpenAI
    participant Dropbox
    participant Obsidian

    Me->>Notion: Save a YouTube link
    Notion->>n8n: New page event
    n8n->>Apify: Request timestamped transcript
    Apify-->>n8n: Transcript segments
    n8n->>OpenAI: Generate transcript based breakdown
    OpenAI-->>n8n: Timestamped summary and wiki links
    n8n->>Dropbox: Upload Markdown note
    Dropbox-->>Obsidian: Sync note into vault
```

The workflow handles videos one at a time. It prepares the transcript with timestamps, asks the model to omit sponsorships and calls to action, then adds video metadata and an embed to the note.

## Example

The Notion table is the video queue:

![Notion database used to queue YouTube videos](assets/notion-queue.png)

The generated note presents a timestamped summary with metadata and inline links to related concepts:

![Example Obsidian note with linked concepts](assets/obsidian-output.png)

## Tech stack

- **Workflow:** n8n running locally
- **Video queue:** Notion
- **Transcript retrieval:** Apify YouTube transcript actor
- **Summary generation:** OpenAI Chat Model
- **Note delivery:** Dropbox sync
- **Knowledge base:** Obsidian

## Import the workflow

The sanitized n8n export is [`workflow/youtube-summarizer.json`](workflow/youtube-summarizer.json). There is no hosted demo because the workflow runs on my local n8n instance and uses my connected services.

1. Import the JSON into your n8n instance.
2. Select your Notion database and connect a Notion account.
3. Add your Apify, OpenAI, and Dropbox credentials.
4. Set the Dropbox destination to a folder synced with your Obsidian vault.
5. Review the output mapping and provider costs before activating the workflow.

The export does not include credentials, pinned records, personal database identifiers, or instance metadata. It uses `/Obsidian/YoutubeNote/` as its Dropbox destination path.

> **Export limitation:** The supplied JSON's final `Edit Fields` node does not include the LLM response in its `Output` field. Connect the model text output there before importing it for use. The screenshots show the result from the working workflow; the export snapshot does not reproduce that output without this mapping.

## Design choices

- **Transcript grounded:** the model is told to use the transcript as its source and not invent timestamps or claims.
- **Useful content first:** sponsorships, self promotion, and calls to action are excluded from the breakdown.
- **Local workflow:** n8n runs on my own host; the connected providers handle transcript retrieval, language processing, and file sync.
- **Notes that connect:** relevant concepts use Obsidian wiki links so the ideas can connect with the rest of the vault.

## Requirements

An n8n instance, a Notion database with YouTube links, Apify access, an OpenAI account, and a Dropbox folder synced with an Obsidian vault. Transcript retrieval depends on captions being available for each video. Review provider pricing and limits before use.
