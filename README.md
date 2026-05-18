HARQ in non-terrestrial links
Interactive animation showing the ACK/NACK exchange between a User Equipment and a non-terrestrial network satellite. Companion piece for Reference Plane, Episode 1, the technical podcast hosted by Daniel Bogdanoff and produced with Rohde & Schwarz.
Live: reference-plane-harq.vercel.app
What it shows
HARQ (Hybrid Automatic Repeat reQuest) works well in terrestrial 5G because the round-trip is short, a handful of HARQ processes is enough to keep the pipe full, and a NACK costs about a millisecond. In a non-terrestrial link, the round-trip can be 3 ms for LEO Starlink, 55 ms for MEO, 480 ms for GEO. Run a fixed number of HARQ processes against those numbers and the UE runs out of process IDs before the first feedback comes back. The link stalls. That stall is the point of this visual.
The animation simulates the protocol slot by slot:

The UE sends a transport block on a free HARQ process.
The block flies up the uplink lane for one one-way delay.
The gNB decodes, generates ACK or NACK, sends it back down.
The UE either frees the process (ACK) or retransmits on the same process (NACK).
Meanwhile, other HARQ processes are sending in parallel.

When all processes are occupied and no feedback has come back yet, the UE waits. The UE WAITING overlay glows yellow and the stall counter ticks up.
Controls
ControlWhat it changesLink scenarioTerrestrial, LEO Starlink, LEO OneWeb, MEO, GEO. Sets the round-trip time and auto-picks a sensible playback speed.BLERBlock error rate, 0–50%. Sets the probability of a NACK on each transmission.HARQ processes1, 8, 16, 32, 64. The pipeline depth. Standard 5G is 16; NTN releases extend this.Speed0.01×, 0.1×, 1×, 10×. Real-time would be too fast to watch on LEO and too slow on GEO; this maps wall time to sim time at a fixed slot rate that you scale.PauseFreeze the simulation.
Every panel collapses, so you can hide the parts you don't need during a screen recording or webinar.
What to look for
A few starting points:

Default boot state (LEO OneWeb, 0.1×, HARQ 8, BLER 10%): pipe is at the saturation point. Any NACK retransmission tips it into a stall. This is the "almost works" demo.
GEO with HARQ 16: the standard 5G HARQ count. Watch it collapse. The pipe needs 480 slots of depth, you have 16.
HARQ 1 with anything: stop-and-wait ARQ. The bank holds one cell, the UE sends one TB, waits a full RTT for feedback, sends the next. Useful for explaining why parallel HARQ exists.
BLER 30%+ at LEO: retransmissions chain together on the same process, occupying it for multiple RTTs each. You can hear yourself say "this is why NTN needs adaptive MCS" out loud.

Tech
Single static HTML file. Inline CSS, inline SVG, inline JS. No build step, no dependencies, no analytics, no tracking. Open index.html in any browser; it works offline. The file is ~40 KB.
This repo is wired up for Vercel auto-deploy: pushes to main go to production, pushes to other branches get preview URLs.
Math, briefly
The simulation uses these constants and simplifications:

Slot time: 1 ms. One transport block per slot, per UE, on the first free HARQ process.
One-way delay: RTT / 2. Geometry is folded into the scenario presets (the NTN Physics Explorer in Episode 1 derives slant range from altitude and elevation; here we collapse that into round numbers).
Pipeline utilization: min(1, N × t_slot / RTT). The fraction of Shannon throughput a HARQ-bounded stop-and-go pipeline achieves. The same formula appears in the NTN Physics Explorer's rate panel.
NACK on retransmission: modeled at BLER × 0.6 to crudely represent the gain from incremental redundancy (chase combining). Real systems do better; the number is for intuition, not for link-budget work.
Max retransmissions: 3, after which the TB is dropped and the process is freed.

What's not modeled: CQI feedback, MCS adaptation, blind retransmissions, asynchronous HARQ timing, the difference between uplink and downlink HARQ behavior in NR, scheduling delay at the gNB, NTN-specific timing advance, or anything related to the actual NR HARQ feedback codebook. If you need any of that, you need a real system simulator, not this.
Related Reference Plane materials

Episode 1: NTN Physics Explorer. Sister applet covering delay, path loss, Doppler, and achievable rate as functions of altitude and elevation. Same brand grammar, same math conventions.
Podcast: Reference Plane (replace with actual URL).
Rohde & Schwarz NTN test solutions: rohde-schwarz.com (replace with actual URL).

Embedding
To embed in a webinar landing page or article, use an iframe:
html<iframe
  src="https://reference-plane-harq.vercel.app"
  width="100%"
  height="1400"
  style="border:0;background:#000c1e"
  loading="lazy"
  title="HARQ in NTN, interactive animation">
</iframe>
The dark background matches the applet's #000c1e body color so there's no seam. Height depends on whether the user has collapsed any panels; 1400 px fits the default state with room to spare.
If the embedding origin needs to be allow-listed, set the appropriate header in vercel.json. The current vercel.json permits embedding from any origin; tighten it before going to production if that matters for your context.
License
Code is MIT. Brand elements (Rohde & Schwarz colors, Reference Plane wordmark) are not.
Credits
Built for Reference Plane by Daniel Bogdanoff.
