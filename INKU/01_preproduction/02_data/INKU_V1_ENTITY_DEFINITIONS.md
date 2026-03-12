# INKU V1 — Entity Definitions

## Slot
A persistent ownership container that anchors one slime within the player's limited collection space.

## Owned slime
A canonical slime record that belongs to the player, has identity, value, traits, and persistence independent of current scene rendering.

## Prairie-active slime
An owned slime whose slot flag marks it as present in the prairie scene. It remains owned by its slot and continues to participate in passive value.

## Current incubator offer
The current slime currently presented by the incubator for purchase. It is temporary but must remain coherent across short session interruptions until rotation expiry.

## Synthetic state
A user-facing readable label derived from internal mental and dynamic state data.
