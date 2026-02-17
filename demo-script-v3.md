# UNS 101 Live Demo — Script & Talking Points

## Target Duration: 30–45 minutes

## Preparation
### Windows to have open
- MQTT Explorer, connected to virtual factories
- Node Red #1 - air compressor
- Node Red #2 - fuel gas meter
- Data Ops Diagram open
- Timebase trend open
- Github:
  - SM-Profiles https://github.com/JPISolutions/SMProfiles
  - DataOps tools https://github.com/JPISolutions/ProveItTools
  - UNS to Dashboard https://github.com/JPISolutions/UNS-to-Framework

---

## SEGMENT 1: Set the Stage

### Opening Problem Statement
> "Most manufacturers we talk to want to leverage AI and advanced analytics — predictive maintenance, energy optimization, autonomous quality control — but they don't know where to start. The technology promises of AI/LLMs are compelling, but without the foundational infrastructure in place, these goals cannot be realized. Data remains trapped in silos, assets are disconnected, and there's no standard architecture to build on. Today we're going to show you where to start."

### The Problem
- Reality for manufactures is:
  - A mix of brand-new PLCs and 20-year-old RTUs
  - Critical equipment running with little to no remote monitoring
  - Data trapped in local HMIs, proprietary historians, or not captured at all
  - Operations have a gut feel on what isn't running properly, but no data to back it up to
  - no knowledge of even where to begin...

- **Key line:** "You can't analyze data you don't have. You can't feed an AI model with data locked inside a PLC where its only connection is via serial. Manufacturers need a practical, guided path to start their digital journey — and that starts with the basics: **connect, collect, store, visualize**."

### What is a UNS? (90-second explainer)
- Single event-driven data hub — every system publishes and consumes from one place
- Think of it like a shared bulletin board: anyone can post, anyone can read, nobody has to know about each other
- Built on MQTT as the transport layer
- Data starts raw, then gets structured and re-published as Sparkplug B with ISA-95 hierarchy — that's the DataOps step
- **Key line:** "The UNS isn't a product you buy. It's an architecture decision. Today we'll show you how simple it is to start — and how this becomes a repeatable pattern you can scale across your entire operation."

### Introduce the Demo Plan
> "We're going to walk through two real scenarios and hypothetical problems on Enterprise A. Scenario 1: modern hardware, brand-new edge device, reading an air compressor. Scenario 2: legacy hardware — a SCADAPack RTU that's already in the field — and a $75 Raspberry Pi bridging it into the UNS. Different equipment, different protocols, same pattern, same result."

---

## SEGMENT 2: Show the UNS Broker & Explain the Data Flow 

Before we look go into the details of our two scenario, I'll breifly run over the data flow from edge to broker to UNS.

**SHOW DATA FLOW DIAGRAM**

### MQTT Broker Dashboard Walkthrough
- Open MQTT Explorer management console
- Explain that this broker holds **two topic spaces**:
  1. **Raw MQTT topics** — where edge devices publish data in whatever format is convenient
  2. **Sparkplug B topics** — where structured, ISA-95-compliant data lives after DataOps, published under the **"jpi" topic space**
- **Walk the topic tree live:** expand the raw topic structure, show the hierarchy, explain the naming convention
- Then switch to the Sparkplug B / "jpi" topic space — show how the structure differs, point out the ISA-95 hierarchy levels
- **Key line:** "You're looking at raw data on the left, structured data on the right. Same broker, same infrastructure. The difference is what happens in the middle — that's the DataOps layer."

### Explain the DataOps Pattern
> "A key concept for today. Getting data into the broker is **connect and collect**. But raw data isn't structured data. What makes a UNS powerful is the transformation step — the DataOps — that lets you **store** meaningful historical data and **visualize** it."

Walk through the pattern:
1. **Edge devices publish raw MQTT** — plain topics, simple payloads, easy to implement
2. **Ignition subscribes via MQTT Engine** — pulls the raw data in for processing
3. **Ignition performs DataOps** — transforms, maps to ISA-95 hierarchy, applies SM-Profiles
4. **Ignition re-publishes via MQTT Transmission** — structured data goes back to the broker under the **"jpi" topic space** as Sparkplug B
5. **Ignition re-consumes the Sparkplug B data** — JPI framework dashboards use the structured data for visualization

- **Key line:** "The edge devices don't need to know anything about ISA-95 or Sparkplug B. They just push raw data. The intelligence is in the DataOps layer — and that's where Ignition earns its keep."

---

## SEGMENT 3: Describe the Mini Prove It 

### Scenario 1 — Instrument Air Compressor

### Set the Scene
> "Scenario 1: Enterprise A requires compressed air for proper plant operation. The air compressor is critical — if it goes down, the plant incurs serious plant-wide downtime. But right now, nobody knows it's down until someone walks by and notices. Let's fix that."

### Show the Hardware
- **Physical:** Hold up / point to the Opto 22 Groov RIO2
- Explain: modern edge I/O module, reads hardwired signals (4-20mA, discrete, thermocouple, etc.)
- Built-in web server, runs Node-RED natively — no separate PC required
- Take a moment to show the physical wiring or a close-up if available — make it tangible
- **Key line:** "This is the **connect** step. This device reads the I/O and publishes plain MQTT. It doesn't need to know about Sparkplug B or ISA-95. Keep the edge simple."

### Walk Through the Node-RED Flow (LIVE)
- Switch to the Groov RIO2's Node-RED interface in a browser
- Walk through the flow **deliberately** — don't rush:
  1. **Input nodes:** Reading compressor run status (discrete), discharge pressure (analog), temperature
     - Explain what each input represents physically on the compressor
  2. **Transform nodes:** Basic scaling/formatting — data is wrapped in an existing SM-Profile
     - Open the SM-Profile payload briefly — show the structure, the metric names, the data types
     - "This is an existing SM-Profile — we didn't have to build this from scratch. It defines what data an air compressor should report."
  3. **Output node:** Plain MQTT publish to HiveMQ (e.g., `raw/enterprise-a/air-compressor/...`)
     - Show the topic string, explain the naming choice
- **Key line:** "Node-RED is free, open-source, and visual. This flow was built in under an hour. It reads I/O, wraps it in an SM-Profile, and sends plain MQTT. That's the **collect** step."

### Scenario 2 — SCADAPack Gas Meter

### Transition
> "That was modern hardware with native MQTT support. But what about the stuff that's already in the field? Legacy equipment that speaks Modbus or DNP3 and has no idea what MQTT is? That's Scenario 2."

### Set the Scene
> "Enterprise A has a high-powered gas furnace for heating glass and raw materials. The customer thinks they're spending too much on natural gas, but they have no data to prove it — and no way to correlate usage against their production schedule. There is a SCADAPack RTU on-site reading the gas flow run, but that is as far as the data goes, it is not integrated anywhere."

### Show the Hardware
- **Physical:** Point to the SCADAPack RTU and the Raspberry Pi — show them side by side
- SCADAPack RTU: industrial RTU, already installed, simulating a gas flow run via Modbus registers
- Raspberry Pi: ~$75, running Node-RED, acts as a protocol bridge
- Take a moment to let that sink in — hold up the Raspberry Pi
- **Key line:** "A $75 Raspberry Pi just turned a legacy RTU into a UNS-connected device. Same pattern — publish plain MQTT."

### Walk Through the Node-RED Flow (LIVE)
- Switch to the Raspberry Pi's Node-RED interface
- Walk through the flow **deliberately**:
  1. **Input nodes:** Modbus read — polling registers from the SCADAPack RTU for gas flow rate
     - Explain what Modbus polling looks like — register addresses, poll intervals
     - "This is the protocol bridge. The Pi speaks Modbus to the SCADAPack and MQTT to the broker. It translates between two worlds."
  2. **Transform nodes:** Convert raw register values to engineering units, wrap in a **custom SM-Profile built by JPI Solutions**
     - **Open the custom SM-Profile and walk through it:** show the metric definitions, explain what JPI chose to include and why
     - "For Scenario 1, we used an existing SM-Profile. Here, JPI Solutions built our own for the gas meter. This profile tells any consumer exactly *what* data is available from this asset. That's the power of SM-Profiles: they're extensible, and you can build your own."
  3. **Output node:** Plain MQTT publish to HiveMQ (e.g., `raw/enterprise-a/gas-meter/...`)
- Highlight the **pattern is identical** to Scenario 1: Read → Wrap in SM-Profile → Publish plain MQTT
- **Key line:** "Different source protocol, different hardware, same output. Plain MQTT into the broker. The edge stays simple."

### Quick Note on SM-Profiles
- SM-Profiles give us a standardized way to describe an asset's data so any consumer can understand *what* data is available
- They're applied at the edge in the MQTT payload — the DataOps layer then validates and maps them into the ISA-95 structure
- "It's like receiving a box of parts with a packing list. The SM-Profile is the packing list — it tells every downstream consumer exactly what's in the box."
- Mention: "In Scenario 1 we'll use an existing SM-Profile. In Scenario 2, JPI Solutions built a custom one. We'll look at both."

---

## SEGMENT 5: Enterprise-Wide DataOps


- **REFER BACK TO DATA FLOW DIAGRAM**
  - We've now just walked through connect, collect but before we consider storage, we want to ensure that the data is in a structured format prior to storing.  In order to do this we will need to perform DataOps on the raw MQTT data to get it back into the UNS as structured data.

### Show Raw Data Arriving in the Broker
- Switch to MQTT Explorer — show the raw MQTT topics populating
- Show the raw payloads — expand one, walk through the SM-Profile fields
- Compare what you see here to what the Node-RED output node was sending — confirm the data arrived intact
- **Key line:** "Data is in the broker. But it's raw. Now let's make it useful for historical **Store**."

### Show Ignition DataOps
- Switch to Ignition
- Show **MQTT Engine** subscribing to the raw MQTT topics
- Walk through the DataOps transformation **step by step**:
  - Show the raw values as they arrive in Ignition
  - Show the mapping to the ISA-95 structure of the existing Enterprise A UNS — explain the hierarchy levels (Enterprise → Site → Area → Equipment)
  - Show the SM-Profile metrics being validated (naming, units, data types)
  - Show the structured data being re-published back to HiveMQ via **MQTT Transmission** as Sparkplug B under the **"jpi" topic space**
- **Side-by-side moment:** If possible, show MQTT Explorer raw topic and HiveMQ "jpi" topic space simultaneously — same data, before and after DataOps
- **Key line:** "This is where raw becomes structured. MQTT Engine pulls the data in, Ignition transforms it to fit Enterprise A's ISA-95 hierarchy, and MQTT Transmission re-publishes it as Sparkplug B under the 'jpi' topic space. Same broker, different topic space, completely different level of data quality."

### Data Store - Ignition OR Timebase Historian
- With the data in the desired structure, we then enable history collection whcih we have demonstrated using the built-in Ignition historian, as well as piping data to a free historian, Timebase (more on this later...)
- Show pre-built timebase trend for compressor in Enterprise A

---
## SEGMENT 6: Visualize

### Walk Through the Architecture
> "Let me recap what's happening — the connect, collect, store in action:"

1. **Connect:** Two completely different edge devices — a Groov RIO2 reading hardwired I/O and a Raspberry Pi polling a SCADAPack over Modbus
2. **Collect:** Both publish plain MQTT with SM-Profiles — simple payloads, minimal edge complexity
3. **Store:** Ignition performs DataOps on both via MQTT Engine — subscribes to raw data, transforms to fit the common UNS structure, stores historical data, and re-publishes as Sparkplug B via MQTT Transmission under the "jpi" topic space
  - show timebase database displaying trending data
4. **Visualize:** Next up - JPI framework dashboards consume the Sparkplug B data — same dashboards, same experience, regardless of source

## Ignition Framework UDTs
- First, a quick not on how JPI's Ignition framework dashboards work.  We've built some scripting/AI tools that will analyze each workcell/equipment that will be displayed on a dashboard and generate UDTs.  These UDTs reference a UDT Perspective view, which will be used to automatically build out the framework dashboard.  This will be shown shortly...

### Show the Final Consumption
- Show Ignition re-consuming the Sparkplug B data from the "jpi" topic space
- Show JPI framework dashboard: compressor run status indicator, pressure trend, alarm configuration, uptime/downtime metrics
- Walk through the dashboard elements — explain what each one tells an operator
- **Trigger a simulated event:** toggle the run status, show the alarm fire, show the uptime/downtime metric update
- Let the event play out — don't rush past it. Let the audience see the real-time response.  Wait and have the compressor dashboard open and show the compressor status alarm.
### Addressing the Value to the Business
- Operations is made aware if air compressor is in abnormal state, saving possible downtime and money.
- Fuel gas is monitored, and Operations doesn't need to go by "gut" feel any more, they have the data to back their thoughts up.

- **Key line:** 
  - "From no monitoring to real-time alarming and uptime/downtime tracking. Connect, collect, store, visualize — done. One edge device, one Node-RED flow, one DataOps layer."
  - "Same DataOps pattern, same Sparkplug B output under the 'jpi' topic space, same dashboard framework. The consumer doesn't know or care that this data started on a Modbus RTU."


### Summary 
- **Scenario 1:** Modern edge I/O, single device, existing SM-Profile
- **Scenario 2:** Legacy RTU + $75 bridge, reuses existing infrastructure, custom SM-Profile built by JPI Solutions
- **Common thread:** Plain MQTT at the edge, Ignition DataOps in the middle, Sparkplug B under "jpi" at the top

### Enterprise-Wide Application
> "We've mostly focused on Enterprise A for these two scenarios. But this same DataOps pattern — subscribe, transform, republish, visualize — works across Enterprise A, B, and C. The pattern is repeatable. Once you've done it for one asset, you can do it for the next. And the next."

### What About Additional Assets?
> JPI's framework has been built with quick deployment in mind.  Assets that are added to the tag provider are automatically added to the system navigation/
- For example, say we get a second filling line for ENterprise B - Site 3. (copy paste the existing line01, rename to line02 and reoload)
- Run and refresh - no need to spend time on building dashbaords/graphics.
- Using strucutred data in an UNS makes building out of the framework quick and efficient.  Less time spent building tags, grachics and more time spent addressing REAL operational issues.

> "Whether you're wiring up a brand-new sensor or unlocking data from a 15-year-old RTU, the pattern is the same. Keep the edge simple — just get data into the broker. Let Ignition handle the heavy lifting of structuring and standardizing. You don't need to rip and replace. You just need a bridge and a DataOps layer."

---

## SEGMENT 7: "Now You're Ready"

### The Foundation
> "So — back to AI and analytics. Everyone wants it. But here's what it actually needs:"
- Structured, real-time data (✅ Sparkplug B with SM-Profiles — produced by DataOps)
- A single accessible data layer (✅ UNS via HiveMQ)
- Consistent schemas that software can consume without custom parsing (✅ ISA-95 hierarchy)
- Separation of raw acquisition from structured consumption (✅ the DataOps pattern)
- Historical data for analysis and model training (✅ stored by Ignition)

> "This is the digital infrastructure that supports AI and analytics. Without it, no AI model in the world can help you. But once you've built this foundation — even starting with just one or two assets — you're ready.  Start with one asset. One edge device. One Node-RED flow. Prove it works — then use that same repeatable pattern to scale"

### What Comes Next...
- Predictive maintenance: model compressor failure patterns from uptime/downtime data
- Energy optimization: correlate gas usage with production output using real flow data
- Natural language queries: "What was the average gas flow rate during last Tuesday's night shift?"
  - Demo Claude Desktop with Timebase - show compressor dashboard and compressor shutdowns.
- **Key line:** "Connect, collect, store, visualize — that's the foundation. Everything you saw today used low-cost, off-the-shelf hardware and software. AI and analytics come next. And this proof-of-concept shows how quickly you can deliver value, prove the pattern works, and secure buy-in for broader deployment."
---
