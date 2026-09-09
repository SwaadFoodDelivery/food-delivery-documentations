# Notification center walkthrough

The authenticated notification center is available at `/notifications` for every signed-in role. It reads recipient-scoped in-app notifications and shows an unread count.

- `GET /api/v1/notifications?unread=false&limit=20` loads the latest notifications.
- `PATCH /api/v1/notifications/:notificationId/read` marks one notification read and cannot access another recipient's notification.
- `POST /api/v1/notifications/read-all` marks the signed-in recipient's unread notifications read.

The local demo seed adds one unread operations notification and two unread driver notifications. These are deterministic mock fixtures; NATS event generation and external email/SMS delivery are not claimed.
