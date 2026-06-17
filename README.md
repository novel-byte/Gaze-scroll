# Gaze-scroll

Scrolling a page by looking at it.

**Status: not yet implemented.** This repository holds the design and the
intended behaviour. The code is not written yet.

## The idea

Reading a long page means scrolling, and scrolling means a hand on the mouse.
For most of a day that is fine. For the times it is not — a broken arm, a
desk full of notes, a keyboard that is already busy doing something else —
it is the only thing standing between you and the text.

Gaze-scroll removes the hand. Your eye position drives the scroll, so the
page moves when you look at it and stops when you look away. The page
follows your attention.

## How it works

**The gaze is tracked.** The browser's camera watches your eyes, and the
position of your gaze is estimated on the screen.

**Your gaze maps to a scroll direction.** Looking below the middle of the
screen scrolls down, looking above it scrolls up. The further from the
centre you look, the faster it goes, so a small glance is a slow drift and
a definite look is a quick run.

**Looking away stops it.** The moment your gaze leaves the screen, the page
stops. This is the important behaviour: the page moves when you are reading
it and not when you are not. Nothing needs a click, and nothing runs away
while you are thinking.

**It is damped.** Scroll velocity is smoothed, and the page does not jerk
when your gaze jumps. A reader should not feel the difference between
reading and not reading — the page should simply not move when they stop.

## Calibration

Eye tracking needs to know where you are looking relative to the screen, and
that is not the same for everyone. So there is a short calibration step:

- Look at a few marked points on the screen, in sequence
- The tracker works out the offset between where you are looking and where
  the camera thinks you are looking
- That offset is applied from then on

Calibration takes under a minute and is stored on the machine. It only needs
redoing if the lighting changes a lot, or the camera moves.

## The failure modes

This is the part that decides whether the idea is usable, so it is worth
being blunt about.

**Blinking.** A blink closes the eyes for a moment, and a naive tracker reads
that as a large downward movement. The fix is to require the movement to
persist across a few frames before it counts, which filters out blinks
entirely.

**Looking away to think.** A pause in reading looks the same as looking
away. The page stops, which is the right behaviour but not always what you
wanted. A short dwell, before the page gives up waiting, avoids the
awkwardness of the page stopping every time you look up to think.

**Losing the track.** Low light, a turned-off screen, a face partly out of
frame — the tracker loses the eyes. The page should stop, not keep going on
a stale reading. Losing the track has to fail safe, always towards stopped.

**Turning the head.** Turning away from the screen to reach for a mug is
not the same as reading, and a tracker that cannot tell the difference will
keep scrolling while you are not there. The head position is a useful
secondary signal here.

**Double screens.** On more than one screen, the calibration has to cover
all of them, or the mapping is wrong on the second.

## What it is not

- **Not a replacement for a mouse.** It is for when reaching for the mouse
  is the problem, not for everyday use. A mouse is faster and more precise.
- **Not a webcam recorder.** The camera is used to estimate where you are
  looking, and the frames are processed and discarded. Nothing is stored,
  and there is no video to leak.
- **Not medical or diagnostic.** It is a reading aid.

## What it would be written in

TypeScript, in the browser. The tracking is the hard part and the parts that
do it well are large; the scrolling side is small.

## Licence

MIT.
