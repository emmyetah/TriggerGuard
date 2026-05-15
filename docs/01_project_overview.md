# TriggerGuard: Scheduled Task Malware Risk Detection Tool

## Project Overview

TriggerGuard is a Windows security tool designed to analyse scheduled tasks and identify potentially suspicious or malicious behaviour. Windows Task Scheduler is commonly used for legitimate automation, but it can also be abused by malware for persistence, privilege abuse, stealth execution, and recurring command execution.

The aim of this project is to build a dashboard-based system that scans scheduled tasks, extracts relevant task metadata, applies rule-based and fuzzy risk analysis, and assigns each task a risk level. The tool is intended to support security awareness by making scheduled task behaviour easier to inspect, understand, and prioritise.

## Main Goal

The main goal of TaskGuard is to detect potentially malicious scheduled tasks by analysing task properties such as task name, executable path, command arguments, trigger behaviour, author, run privileges, and file location.

## Key Features

- Scan Windows scheduled tasks
- Extract task metadata
- Detect suspicious task names, paths, commands, and triggers
- Assign risk scores to each task
- Display tasks in a dashboard
- Show low, medium, high, or critical risk levels
- Provide explanations for why a task was flagged
- Support future extension into fuzzy logic or machine learning-based detection

## Target Users

The target users of the system include:

- Cybersecurity learners
- System administrators
- Windows users who want more visibility into scheduled tasks
- Security analysts investigating persistence mechanisms
- Students building practical cybersecurity tools

## Project Motivation

Scheduled tasks are a common persistence mechanism used by malware and attackers. Although Windows provides Task Scheduler and command-line tools for viewing tasks, these tools do not provide a simple visual risk-based dashboard focused specifically on suspicious scheduled task behaviour. TriggerGuard aims to fill this gap by providing a focused, explainable, and beginner-friendly scheduled task analysis tool.

## Expected Outcome

The expected outcome is a working Windows desktop or web-based dashboard that can scan scheduled tasks, classify their risk level, and explain why each task may be safe or suspicious.