<!---
Shields.io Github badges!
-->

![GitHub release (latest by date including pre-releases)](https://img.shields.io/github/v/release/4Luke4/Sleep-Fix?color=yellow&include_prereleases)
![Platform](https://img.shields.io/static/v1?label=platform&message=windows%20%7C%20macOS%20%7C%20linux%20%7C%20Project%20Infinity&color=informational&style=flat)
![Language](https://img.shields.io/static/v1?label=language&message=All&color=teal&style=flat)
![Supported games](https://img.shields.io/static/v1?label=supported%20games&message=BG%3AEE%20%7C%20BG2%3AEE%20%7C%20IWD%3AEE%20%7C%20EET&color=dodgerblue&style=flat)

![GitHub repo size](https://img.shields.io/github/repo-size/4Luke4/Sleep-Fix?style=flat)
![GitHub all releases](https://img.shields.io/github/downloads/4Luke4/Sleep-Fix/total?color=cyan)
![GitHub release (latest by date including pre-releases)](https://img.shields.io/github/downloads-pre/4Luke4/Sleep-Fix/latest/total?color=cyan)
![Maintenance](https://img.shields.io/static/v1?label=maintained%3F&message=yes&color=greenlight&style=flat)
![GitHub contributors](https://img.shields.io/github/contributors/4Luke4/Sleep-Fix?color=blueviolet&style=flat) [![Contributors Display](https://badges.pufler.dev/contributors/4Luke4/Sleep-Fix?size=30&padding=5&bots=true)](https://badges.pufler.dev)

![GitHub issues](https://img.shields.io/github/issues/4Luke4/Sleep-Fix?color=red&style=flat)
![GitHub pull requests](https://img.shields.io/github/issues-pr/4Luke4/Sleep-Fix?color=brown&style=flat)

##

<div align="center"><h1></a>Sleep Fix</h1></div>

*Accurately granting immunity to just one form of an opcode.*

[![Beamdog Forums](https://img.shields.io/static/v1?label=Discussion&message=Beamdog%20Forums&color=444&labelColor=eee&style=for-the-badge)](https://forums.beamdog.com/discussion/82729/ "Beamdog Forums")

## Overview
Opcode 39 (Sleep). It is used for: Sleep, Unconsciousness, Knockdown, Hopelessness, and Nausea (Poison).
So:
- Take all related effects in every sleep spell and move them to external EFF files, giving them all the same parent resource (EFFECT_SLEEP). Most of these spells could share EFF's, as the variations (saving throws, min/max hd/level, magic resistance, power, probability, etc...) would be put in opcode 177, not the EFF.
The EFF file would mainly have: `Opcode`, `parameter1/2/3` (as appropriate), `resource1/2/3` (if necessary), `special`, `probability1=100`, `dicenumber/dicesize` (as appropriate), `parent_resource=(EFFECT_[TYPE])`. All other fields would be zero/empty.
- For creatures that should be immune to "Sleep", replace `Opcode 101/Parameter2=39` (Immunity to effect: opcode 39) with the following effect:
```
opcode=318, target=2, timing=9, probability1=100, resource = (EFFECT_SLEEP)
```
Do the same for Unconsciousness, Knockdown, Hopelessness, and Nausea (Poison).
- The undead rings, for example, would end up with:
```
opcode=318, target=2, timing=2, probability1=100, resource = (EFFECT_SLEEP)
opcode=318, target=2, timing=2, probability1=100, resource = (EFFECT_UNCONSCIOUS)
opcode=318, target=2, timing=2, probability1=100, resource = (EFFECT_HOPELESS)
opcode=318, target=2, timing=2, probability1=100, resource = (EFFECT_POISON)
```
This way they would remain vulnerable to knockdown, such as from Wing Buffet.
- Similarly, the EFF's for any effects (even damage) that operate through poison (Stinking Cloud, Cloudkill, Poison) would use the (EFFECT_POISON) parent resource.
While items that grant Poison Immunity would just use:
```
opcode=318, target=2, timing=2, probability1=100, resource = (EFFECT_POISON)
```
- Most every related spell would share the same EFF files, since all variation will be in the opcode 177 effect that calls them (saving throws, min/max hd/level, magic resistance, power, probability, etc...), not in the EFF (these fields should be left blank/zero in the EFF). Two sleep spells would use mostly the same EFFs, while a color spray would use another batch of EFF's (for unconsciousness). Some effects would have extras or alternates (mostly visual/audio/lighting effects for different spells).
- Because all `Display String` effects would use the same parent resource for a given purpose, there would be no need to look up string references for each game, or make sure every variation is blocked when granting immunity. Just select the EFF with the correct string for use in a spell, or block all appropriate variations with opcode 318 when granting immunity.
## Limitations
- EFF's called through opcodes 248, 249, and 272 would have to remain separate from these.
- Opcode 337 (remove effects by opcode) would falter against these, but all other effect removal would continue to work as they currently do.
- Unlike opcode 337, direct effect removal effects (cure sleep, remove paralysis) do detect those effects in external EFF files.
- Spell school/type protection removal would still look at the school/type in opocde 177, not the EFF.
- Opcode 321 would still remove effects based on the parent resource of opcode 177, not the custom one defined in the EFF.
- Deflection/Reflection would not change, with the exception of opcode 198 (reflect specified effect), which was never really viable specifically because these opcodes have multiple purposes.
- This could be a small addition for some effects or require complete replacement overhaul for others.