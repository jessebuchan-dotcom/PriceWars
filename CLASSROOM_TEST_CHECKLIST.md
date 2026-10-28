# Classroom pilot checklist — complete after deployment

**Do not use a preview or a single browser tab as evidence that multiplayer has been tested.**

## Setup and access

- [ ] Teacher can create a room using the private server PIN.
- [ ] An incorrect PIN fails and the PIN never appears in any student's browser.
- [ ] QR code and room code open the correct public joining page on an independent smartphone.
- [ ] Students can join without a Google account or email address.
- [ ] Duplicate nicknames are rejected; classmates can join with different names.
- [ ] Teacher can reassign students and remove a student before starting.
- [ ] The private teacher key can be copied and used to access teacher controls on a second device.
- [ ] The projector screen displays live data but cannot start/pause/end the market.

## Live gameplay (at least three devices)

- [ ] Two students in the same supermarket see a strategy update made by their teammate.
- [ ] A strategy change from one supermarket is visible to others without refreshing.
- [ ] Pricing affects demand; selling below wholesale can reduce profitability.
- [ ] Advertising increases attraction while increasing expenditure.
- [ ] Insufficient stock causes lost sales, and stock orders deduct cash immediately.
- [ ] Purchased stock arrives only after the correct number of market periods.
- [ ] Higher staffing can serve more customers but increases wages.
- [ ] Periodic profit, revenue, market share and customer reputation update live.
- [ ] The teacher can trigger an industry shock; all devices see the same notification.
- [ ] Pausing stops market trading, market time and student strategy changes.
- [ ] Resuming continues from the paused time without charging for the paused duration.
- [ ] Ending the game freezes the results.

## Reliability and privacy

- [ ] Refreshing a student browser restores the same supermarket and decisions.
- [ ] Temporarily switching off Wi-Fi then reconnecting restores the current game state.
- [ ] A restarted server recovers the room in paused state from Firestore; teacher can resume.
- [ ] A non-teacher cannot submit start/end/shock events to the server.
- [ ] Students cannot directly set their own profit, cash or market share through browser requests.
- [ ] The classroom network allows WebSocket traffic or Socket.IO's long-polling fallback.
- [ ] At least 20 simultaneous devices can participate with acceptable latency.
- [ ] Teacher can export CSV and JSON after the game, without exposing private tokens.
- [ ] Data storage, student nicknames and deletion/retention are approved by the school.

## Classroom debrief

- [ ] Compare the profit winner with the market-share winner.
- [ ] Explain the difference between revenue and operating profit.
- [ ] Analyse how increasing stock, staffing or advertising changed costs.
- [ ] Evaluate a decision made in response to a teacher-triggered market event.
