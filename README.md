# ROTOCAD

![[gear.png]]


Hi :)
This is a text-based polar CAD-ish. Just check specifics.md for docs if you are in a rush, otherwise check below for the general structure of the language and then straight to specifics.md


### General Philosophy
Briefly: I trust the operator, language is non-Turing-complete by design, it is kinda useful only if you apply it to gears or objects with a dominant radial symmetry. My distributions will be atrocious, feel free to build a better one ;)

NOTE: this means that in loop blocks I don't check for infinite loops, I actually came up with a simple mechanism to do so but if I eventually implement custom functions I really can't do anything about it. Long story short: I can't predict how a function with some form of memory behaves so checking a hypothetical dtheta is useless and BREAKS EVERYTHING


### Origin Story
It actually started because I heard somebody talking about "belly" at dinner. I immediately thought of the sheer violence of a transparent horizontal plane slicing my belly up and then I asked myself: how do I draw my insides? The answer was pretty much instantaneous: I need a dynamic radius (ρ) and an angle (θ) that changes over time. Plain and simple.

The interesting thing about this model is that you can really draw anything with that as long as the radius passes through the same point N times and so on and so on. As soon as I realized it, I quickly ate my dinner and launched myself on my bed to formulate what was a text-based 2D drawing tool.

Soon after, the ceiling cracked open and the word "extrusion" hit my head so violently I just had to convert this into a 3D modeling tool. Why waste such an opportunity? Ceiling is fine btw.