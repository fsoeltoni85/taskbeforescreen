# TaskBeforeScreen — Product Requirements

**Status:** Draft for approval  
**Version:** 0.1

## 1. Product Overview

TaskBeforeScreen is an Android-first parental-control application that lets parents assign tasks to children in exchange for screen time.

Parents send instructions in text, audio, or video. Children complete the assigned task and submit evidence such as a photo, audio recording, or video. After the evidence is successfully submitted, the child receives a predefined screen-time allowance.

When the allowance expires, access to ordinary apps is restricted again, and the child returns to the TaskBeforeScreen task screen.

The product prioritizes a simple parent-child workflow over a complex device-management system.

## 2. Product Goals

1. Let parents assign tasks and reward children with screen time.
2. Support children who cannot read through audio and video instructions.
3. Support photo, audio, and video evidence submissions.
4. Deliver submitted evidence to the parent's device.
5. Automatically grant and expire screen-time allowances.
6. Keep the existing Android launcher; do not require TaskBeforeScreen to become the default launcher.
7. Keep the initial implementation simple enough to test and improve one feature at a time.

## 3. User Roles

### Parent

The parent can:

- Create and manage a child profile.
- Pair a child's device with the parent's account.
- Create a task with text, audio, or video instructions.
- Specify the required evidence type.
- Set the screen-time reward duration.
- Review submitted evidence and view task history.
- Receive notifications about submissions and device status.
- Create, replace, or deactivate the active task.

### Child

The child can:

- View or listen to the parent's task instructions.
- Capture the required evidence.
- Submit evidence to the parent.
- See the remaining screen time while access is granted.
- Use the device normally during the granted allowance.
- Return to the task screen when the allowance expires.

## 4. Core User Flow

1. The parent creates a task, selects the required evidence type, and sets a reward duration.
2. The task is synchronized to the child's device.
3. When the child needs to unlock access, TaskBeforeScreen presents the active task.
4. The child reads or listens to the instructions.
5. The child captures and submits the required evidence.
6. Once the evidence upload and submission are confirmed, the backend grants a screen-time allowance.
7. The timer begins when that allowance is granted.
8. When the timer expires, the device returns to the restricted state.
9. The parent can review the evidence and create the next task.

For the initial release, evidence submission does not require manual parent approval before granting screen time. Parent review is available after submission.

## 5. Task Configuration

Each task contains:

- A title.
- Optional text instructions.
- Optional audio or video instructions.
- One required evidence type: photo, audio, or video.
- A reward duration selected by the parent.
- An active, completed, or replaced state.
- Creation and submission timestamps.

Only one task is active for a child at a time in the initial release. The parent can replace the active task.

## 6. Screen-Time Rules

- A reward is granted only after the evidence is successfully uploaded and the submission is confirmed.
- The countdown starts when the allowance is granted, not when evidence capture begins.
- The authoritative expiration time is stored as a timestamp, not just a local countdown value.
- Closing or restarting the app must not reset the allowance.
- When time expires, the device enters the restricted state again.
- The child cannot grant themselves additional screen time by changing the local timer.
- Parent-issued task and allowance changes must synchronize to the child's device.
- The initial release does not include cumulative rewards or complicated schedules.

## 7. Android Restriction Model

The existing Android launcher remains unchanged.

TaskBeforeScreen will initially use an application-restriction approach that detects attempts to open apps while the child has no active allowance and redirects the child to the task screen.

The restriction mechanism must be tested on physical Android devices. An ordinary installed app cannot guarantee a fully tamper-proof lock across every system surface. Settings, notifications, permission revocation, force-stop, device restart, and manufacturer-specific background restrictions are known areas that require testing.

Dedicated-device or kiosk management is outside the initial scope unless testing proves that the MVP restriction model is insufficient.

## 8. Parent-Child Pairing and Authentication

- Parent accounts must be authenticated.
- A child device must be explicitly paired with a parent account.
- Each child device must have a distinct device record.
- A parent may access only children linked to their account.
- A child device may retrieve only its own active task, allowance state, and submission history.
- Pairing must not rely on a publicly guessable child identifier.

The detailed pairing method and authentication rules will be defined in the architecture and security documents.

## 9. Evidence and Media

The initial release supports photos, audio, and videos.

- Media is stored in private storage.
- Each submission records the child, task, media type, storage reference, and submission time.
- Access to evidence is restricted to the linked parent and authorized application workflows.
- Upload failures must not grant screen time.
- Media must not be made publicly accessible by default.
- Retention limits and deletion rules will be specified before production release.

## 10. Offline and Failure Behavior

- The device must preserve the last known allowance expiration time locally.
- An active allowance must continue counting down if the internet disconnects.
- A new reward must not be granted until the evidence submission has been confirmed by the backend.
- If upload fails, the child sees a retry option and receives no new allowance.
- If synchronization fails, the app must not silently extend an existing allowance.
- If the restriction service is disabled or unavailable, the app must show its restriction status when opened. The MVP does not promise protection against every bypass.

## 11. MVP Scope

### Included

- Parent authentication.
- Parent-child device pairing.
- Parent task creation.
- Text, audio, and video instructions.
- Photo, audio, and video evidence submission.
- Private media storage.
- Parent evidence inbox.
- Fixed-duration screen-time rewards.
- Persistent countdown and expiration handling.
- Android restriction and return-to-task flow.
- Basic submission and allowance history.
- Automated tests and GitHub Actions APK builds.

### Excluded

- Custom launcher.
- Fully tamper-proof kiosk enforcement.
- AI evaluation of homework, memorization, or room tidiness.
- Automatic scoring of submitted evidence.
- Complex recurring task schedules.
- Multiple reward currencies or points.
- Detailed app-usage analytics.
- iOS support.
- Subscription and payment features.

## 12. MVP Acceptance Criteria

The MVP is ready for practical testing when:

1. A parent can create a task and set its reward duration.
2. The child device receives the active task.
3. The child can view or play the task instructions.
4. The child can capture and upload the required evidence.
5. The parent can view the submitted evidence.
6. A successful submission grants the configured screen time exactly once.
7. Failed or repeated uploads cannot create duplicate allowances.
8. Closing the app or restarting the device does not reset the expiration time.
9. The device returns to the restricted state when the allowance expires, subject to the documented Android enforcement limitations.
10. Authorization rules prevent one family from reading another family's tasks or evidence.
11. Each completed feature has automated CI results and a downloadable test APK when an Android build is available.

## 13. Delivery Process

Development proceeds one feature at a time.

For each feature:

1. Agree on the relevant documentation and behavior.
2. Implement the feature in the GitHub repository.
3. Run automated checks and build the Android APK using GitHub Actions.
4. Review failures and fix them before declaring the build ready.
5. Share the APK and a concise test checklist.
6. Continue after the user has tested the feature and reported feedback.

Documentation must be approved in chat before being committed to the repository. The Flutter project and CI/CD setup begin only after the documentation set has been approved.
