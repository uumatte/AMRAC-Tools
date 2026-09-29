speed-quarter: speed scale x0.25
===============================

The live flight model with every speed and distance multiplied by 0.25 and
every time left alone: the same fight, 0.25 the size. A turn takes as many
seconds, a climb to the ceiling as long, a missile flies for as long -- over
0.25 of the ground, at 0.25 of the speed.

Generated 2026-09-29 by tools/scale-flightmodel.py from 26.2server/config/amrac/flightmodel.
Checked by `gradlew speedScaleCheck -Pset=speed-quarter -Pscale=0.25`, which steps the
production physics from thousands of matching states and requires the scaled
velocity to be exactly 0.25 x the original and the rates, Mach and g identical.
Regenerate rather than edit: the live documents are retuned often.

INSTALL (the mod reads only config/amrac/flightmodel):

    1. back up   26.2server/config/amrac/flightmodel
    2. copy      speed-quarter/environment.json, aircraft/, missile/, ai/
       over      26.2server/config/amrac/flightmodel/
    3. restart the server. Aircraft documents are picked up live, but the
       atmosphere and the missiles are shared by everything in the air, and a
       half-swapped set is two scales at once.

The environment has to go with the rest. Speed of sound, gravity and air
density are what make a x0.25 aeroplane behave like the original; the aircraft
documents alone on the stock atmosphere fly wrongly, not just slowly.

ai/pilot.json has to go with it as well. The live one asks for full-size
speeds -- a 250 b/s cruise, an 80 b/s rotate, a 250 b/s turn-back -- which a
x0.25 aeroplane reaches late or not at all. This one is the live file with its
numbers scaled, in the same tidy form. pilot-half.json and pilot-quarter.json
are something else: a smaller map flown by full-size aeroplanes. Never put
one of them over this set's pilot.json.

WHAT CHANGED
    speed        x0.25   speeds, speed of sound, structural placards, flap
                         placard, published speeds, radar and seeker
                         velocity gates, seeker inertial drift; the AI's
                         cruise, rotate, turn-back and overspeed-guard speeds
    reference    x0.5    the control law's reference speeds (full authority,
                         control-authority reference, roll-taper onset). The
                         code turns them into a pressure on a fixed 1.225
                         kg/m3 rather than on this set's air, so at x0.25 the
                         law would read every pressure as 4 x what it is --
                         more control slow, less roll fast. x sqrt(0.25) keeps
                         the pressure ratio, and the handling, where it was
    distance     x0.25   altitude (the atmosphere is folded 4:1, so the real
                         14 km sits in the first 3.5 km), missile range, radar
                         lock range, seeker activation and burn-through range,
                         reference-shot altitude; the AI's engagement ranges
                         and its navigation, patrol and border distances
    height       x0.25   the AI's cruise and departure heights, above sea
                         level (Y 63), never under its altitude floor
    gravity      x0.25
    thrust       x0.25   aircraft and missile motors
    air density  x4      so every aerodynamic force is x0.25 with it

WHAT DID NOT
    mass, fuel and burn rate, inertias and control moments, angles, g limits,
    rates in degrees a second, Mach numbers, every coefficient, every
    duration. Also minimum launch range, arming distance and proximity fuse,
    which are about clearing and hitting an aeroplane, and the aeroplanes are
    the same size.

IN THE CODE
    What the code holds for itself it scales from this set's air -- its
    sea-level speed of sound against the real 340 m/s, which makes x0.25 here:
    the AI's speed bands, ranges, height bands and throttle gain, the g its
    load reading assumes and the ceiling it measures; the gun, its range
    and its lead sight; the missile warning and the RWR dial; the radar
    scan; the seeker's chaff and overshoot gates and the chaff and flare
    throws; the runway speeds, the gear placards and the impact a landing
    survives; the auto-throttle and HUD overspeed margins; the AoA
    limiter's lowest speed and the roll an uneven load puts in. That takes
    a mod jar with the speed-scale change of 2026-09-24
    (amrac.physics.aircraft.SpeedScale) on the server AND on every client.
    On an older jar all of those stay full size inside a fight that is not,
    and the set flies wrongly in every one of them.

STILL FULL SIZE, ON PURPOSE
    * The virtual layer's floor (Y 800), and with it the AI's altitude floor
      (Y 1000). Terrain does not shrink, so neither does the height an
      aeroplane nobody is near has to keep. In this set's air that is 3748 m
      up, where it is 937 m at full size: the AI fights relatively high.
      The trainee and elementary ranks cruise on it.
    * Where a store, a chaff bundle or a flare leaves the airframe, and
      everything else that is the size of an aeroplane.
    * The HUD reads speeds in blocks a second, so it reads 0.25 of the full-size
      numbers; Mach is unaffected.
    * Bombs, which fall under Minecraft's gravity rather than the set's.
