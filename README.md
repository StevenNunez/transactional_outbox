# TransactionalOutbox

Sidecar application to add Transactional Outboxes to your application.
More on the pattern [here](https://microservices.io/patterns/data/transactional-outbox.html).

## Flow
The main application's logic will emit events at various states of execution. Those events will be written to an `transcational_outbox` table. This allows rolledback transactions to never falsely emit events.

`TransactionalOutbox` will watch this table for inserted records. When it finds a new records, it locks the row, stores the event in the application's `events` table, and emits the event out to the eventstore. The `transactional_outbox` row is then deleted.

## Expectations

This app requires access to a local database in order to write successfully emitted events to a local store as well as the read the `transcational_outbox` table. The default implementation will be emitting to a Postgres database using the [eventstore](https://github.com/commanded/eventstore) library.

## Usage
* Setup configuration.
  * Credentials for local application
  * Credentials for eventstore
* Tasks
  * Generate new `transactional_outbox` table. (may be do this in your own app to prevent Ecto artifacts from being written)
* On Start
  * Confirm `events` table in local application.
  * Confirm `transactional_outbox` table in local application.
  * Watch `transactional_outbox` table for changes
