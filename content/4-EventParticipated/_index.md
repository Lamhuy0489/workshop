---
title: "Events Participated"
date: 2026-07-25
weight: 4
chapter: false
pre: " <b> 4. </b> "
---

# EVENTS PARTICIPATED

During my internship, I attended the technical gathering organized by the AWS Vietnam Community in Hanoi: **[HANOI] AWS VIETNAM COMMUNITY MEETUP**. This event provided valuable insights into modern AI Agent architectures, real-world engineering practices from seasoned experts, and shaped the architectural design of my internship capstone project.

---

## 1. Event Information

<table style="width: 100%; border-collapse: collapse; margin-top: 15px; margin-bottom: 25px;">
  <thead>
    <tr style="background-color: #232f3e; color: #ffffff;">
      <th style="padding: 12px; border: 1px solid #ddd; text-align: left; width: 25%;">Attribute</th>
      <th style="padding: 12px; border: 1px solid #ddd; text-align: left; width: 75%;">Event Details</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="padding: 10px; border: 1px solid #ddd;"><strong>Event Name</strong></td>
      <td style="padding: 10px; border: 1px solid #ddd;"><strong>[HANOI] AWS VIETNAM COMMUNITY MEETUP</strong></td>
    </tr>
    <tr style="background-color: #f9f9f9;">
      <td style="padding: 10px; border: 1px solid #ddd;"><strong>Date &amp; Time</strong></td>
      <td style="padding: 10px; border: 1px solid #ddd;">08:30 – 12:00 | Saturday, July 25, 2026</td>
    </tr>
    <tr>
      <td style="padding: 10px; border: 1px solid #ddd;"><strong>Location</strong></td>
      <td style="padding: 10px; border: 1px solid #ddd;">AWS Hanoi Office – 7th Floor, Grand Terra Tower, 36 Cat Linh, Dong Da, Hanoi</td>
    </tr>
    <tr style="background-color: #f9f9f9;">
      <td style="padding: 10px; border: 1px solid #ddd;"><strong>Organizing Entity</strong></td>
      <td style="padding: 10px; border: 1px solid #ddd;">AWS Vietnam Community</td>
    </tr>
    <tr>
      <td style="padding: 10px; border: 1px solid #ddd;"><strong>Role</strong></td>
      <td style="padding: 10px; border: 1px solid #ddd;">Attendee alongside 100+ cloud engineers, developers, and tech students</td>
    </tr>
    <tr style="background-color: #f9f9f9;">
      <td style="padding: 10px; border: 1px solid #ddd;"><strong>Guest Speakers</strong></td>
      <td style="padding: 10px; border: 1px solid #ddd;">Ho Viet Anh &amp; Phong Pham (AWS Community), Tuan Vu (OpenClaw), Nguyen Thu &amp; Nam La (Enterprise AI), Henry (Duc) Bui (Product Leader)</td>
    </tr>
    <tr>
      <td style="padding: 10px; border: 1px solid #ddd;"><strong>Key Topics</strong></td>
      <td style="padding: 10px; border: 1px solid #ddd;">The Era of Autonomous AI Agents, Translating AI Trends into Measurable Business Value, Ship Fast with AI, Not by</td>
    </tr>
  </tbody>
</table>

---

## 2. Event Overview Card

<div style="margin-top: 20px; margin-bottom: 30px;">
  <div style="background: #ffffff; border: 1px solid #e1e4e8; border-top: 4px solid #ff9900; border-radius: 8px; padding: 22px; box-shadow: 0 4px 12px rgba(0,0,0,0.06);">
    <h3 style="margin-top: 0; color: #232f3e; font-size: 1.25em;">Event 1: AWS Vietnam Community Meetup Hanoi</h3>
    <p style="margin: 8px 0; font-size: 0.95em; color: #555;"><strong>Date:</strong> 07/25/2026 | <strong>Location:</strong> AWS Hanoi Office, 7th Floor, Grand Terra Tower, 36 Cat Linh, Hanoi</p>
    <p style="margin: 8px 0; font-size: 0.95em; color: #555;"><strong>Focus:</strong> Exploring the OpenClaw open-source AI Agent framework, enterprise AI ROI evaluation, lean product development with the "With AI, Not By AI" mindset, and direct networking at the AWS Hanoi office.</p>
    <div style="margin-top: 18px;">
      <a href="4.1-event1/" style="display: inline-block; padding: 8px 18px; background: #232f3e; color: #ffffff; text-decoration: none; border-radius: 5px; font-weight: bold; font-size: 0.9em;">View Detailed Event Report</a>
    </div>
  </div>
</div>

---

## 3. Accumulated Learnings & Project Mapping

<table style="width: 100%; border-collapse: collapse; margin-top: 15px; margin-bottom: 25px;">
  <thead>
    <tr style="background-color: #232f3e; color: #ffffff;">
      <th style="padding: 10px; border: 1px solid #ddd; text-align: left; width: 25%;">Knowledge Pillar</th>
      <th style="padding: 10px; border: 1px solid #ddd; text-align: left; width: 45%;">Core Meetup Takeaway</th>
      <th style="padding: 10px; border: 1px solid #ddd; text-align: left; width: 30%;">Applied to Capstone Project</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="padding: 10px; border: 1px solid #ddd;"><strong>AI Agent Era</strong></td>
      <td style="padding: 10px; border: 1px solid #ddd;">Moving beyond passive prompt completion to active autonomous agents combining Planning, Memory, and Tool Calling.</td>
      <td style="padding: 10px; border: 1px solid #ddd;">Built an automated document parsing pipeline coordinating local fast-path tools and multimodal vision.</td>
    </tr>
    <tr style="background-color: #f9f9f9;">
      <td style="padding: 10px; border: 1px solid #ddd;"><strong>FinOps &amp; Efficiency</strong></td>
      <td style="padding: 10px; border: 1px solid #ddd;">Selecting engineering solutions according to cost and complexity; eliminating wasteful inference costs.</td>
      <td style="padding: 10px; border: 1px solid #ddd;">Engineered native PyMuPDF fast-path routing (0.31s latency, $0.00 cost), invoking vision models only when needed.</td>
    </tr>
    <tr>
      <td style="padding: 10px; border: 1px solid #ddd;"><strong>Serverless Architecture</strong></td>
      <td style="padding: 10px; border: 1px solid #ddd;">Connecting serverless services for event-driven, decoupled, and horizontally scalable workflows.</td>
      <td style="padding: 10px; border: 1px solid #ddd;">Amazon S3 Event Notification triggering AWS Lambda to log job state into DynamoDB within 214 ms.</td>
    </tr>
    <tr style="background-color: #f9f9f9;">
      <td style="padding: 10px; border: 1px solid #ddd;"><strong>Security &amp; Oversight</strong></td>
      <td style="padding: 10px; border: 1px solid #ddd;">The "With AI, Not By AI" principle: Engineers must maintain architectural ownership, least-privilege access, and data security.</td>
      <td style="padding: 10px; border: 1px solid #ddd;">Encrypted parameter injection via AWS SSM Parameter Store with KMS; managed EC2 via Session Manager without SSH port 22.</td>
    </tr>
  </tbody>
</table>