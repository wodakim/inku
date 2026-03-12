# INKU V1 — Error Cases

## Not enough money
Message should be short and direct:
- "Not enough money."

## All slots full
Message should clarify the next decision:
- "All slots are occupied."
- "Sell or delete a slime to free space."

## Prairie full
- "Prairie is full."
- "Remove a slime before adding another."

## Empty rename
- fallback to default name
or
- cancel with no change

## Selling or deleting a slime that is active in prairie
- remove it from prairie automatically
- then process sale or deletion

## Broken save references
Need a simple cleanup pass:
- clear invalid slot references
- preserve valid slimes
- avoid silent duplication
