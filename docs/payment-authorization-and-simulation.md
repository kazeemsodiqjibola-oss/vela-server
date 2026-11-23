# Payment authorization and simulation boundaries

## Guarded authorization updates

Authorization is persisted with a conditional update from `CREATED` to
`AUTHORIZED`. If a concurrent request has already changed the payment, the
repository raises `PAYMENT_INVALID_STATE`; the service emits no
`payment.authorized` event because no authorization transition occurred.

The event is emitted only after the guarded update succeeds and the updated
record has been read back. This keeps downstream work aligned with durable
state rather than with a request that merely passed its initial checks.

## Simulation is not settlement

The transaction simulation endpoint delegates to the configured Stellar RPC
provider and returns diagnostic simulation data. It does not submit an
envelope, write a payment, index transaction history, or mark a payment as
confirmed. A successful simulation means only that the provider accepted the
simulation request; settlement still requires the normal signed submission,
Horizon confirmation, and persistence flow.

The tests use mocked RPC responses and do not represent a live testnet or
on-chain settlement.
