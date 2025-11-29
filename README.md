# BGP Router (Python)

This project implements a simplified Border Gateway Protocol (BGP) router for a simulated network environment. The router manages multiple neighbor connections, exchanges routing announcements, performs longest-prefix matching, and forwards packets using BGP-style policy constraints.

## Overview

The router communicates with multiple neighbors over UDP sockets using a custom JSON-based control protocol. It processes BGP-like messages including updates, withdrawals, routing table dumps, and data packets.

Key capabilities include:

-   Managing multiple sockets through `select`
-   Processing UPDATE and WITHDRAW messages
-   Maintaining a forwarding table with BGP attributes
-   Longest-prefix-match route selection
-   Full BGP tie-breaking sequence
-   Optional route aggregation and deaggregation
-   Customer/provider/peer routing policy enforcement
-   Forwarding data packets according to best available routes

## Message Types

The router supports the following message types:

-   **handshake** – connection initialization
-   **update** – new route advertisement
-   **withdraw** – withdrawal of a previously advertised route
-   **dump** – request for the router’s full forwarding table
-   **data** – data packet requiring forwarding to a destination IP

All messages use the format:

{
"type": "<message-type>",
"src": "<source-IP>",
"dst": "<destination-IP>",
"msg": { ... }
}

sql
Copy code

## Forwarding Table and Route Selection

Routes are stored with:

-   network prefix
-   netmask
-   AS path
-   origin
-   local preference
-   next-hop peer
-   flags for self-originated routes

When forwarding data packets, the router:

1. Computes binary prefix matches between the destination and each route
2. Selects all routes tied for the longest prefix match
3. Applies BGP tie-breaking rules:

    - highest local preference
    - prefer self-originated routes
    - shortest AS path length
    - origin type preference
    - lowest network IP

This returns the single best route for packet forwarding.

## Route Aggregation

The router merges two routes into a less-specific prefix when:

-   both networks share the same netmask
-   networks are numerically adjacent
-   both routes share the same next hop and attributes
-   aggregation is permitted by policy

Aggregated entries are recorded so they can be deaggregated correctly during withdrawals. If a withdrawn route affects an aggregated prefix, the router rebuilds the entire forwarding table from its history of announcements.

## Customer/Provider/Peer Policy

Each neighbor is labeled as a:

-   customer
-   provider
-   peer

Routing decisions and propagation rules follow simplified economic constraints (valley-free routing):

-   Announcements from providers and peers are forwarded only to customers
-   Data packets are forwarded only if the next hop does not violate customer-provider hierarchy

## Running the Router

python3 router.py <asn> <connections...>

csharp
Copy code

Connections are specified as:

port-neighborIP-relation

makefile
Copy code

Example:

python3 router.py 65001
10000-10.0.0.2-cust
10001-10.0.0.3-peer

pgsql
Copy code

Each connection spawns a dedicated UDP socket, and the router begins exchanging handshake messages and participating in the routing simulation.

## Implementation Notes

-   Uses `select.select` to manage multiple UDP sockets concurrently
-   Routes are stored as Python dictionaries for convenience
-   IP addresses and netmasks are manipulated in both dotted-quad and binary forms
-   Aggregation requires maintaining auxiliary structures to track which prefixes have been merged
-   The router rebuilds state when deaggregation is needed to maintain correctness
