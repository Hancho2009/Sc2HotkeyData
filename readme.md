Data from StarCraft II Version: 4.6.2 (69232)

**Motivation:** Starcraft 2's hotkey editor has two primary issues
-   Certain hotkey conflicts are not being detected
-   Only works within the boundaries of a single game mode at a time. E.g. WoL campaign, LotV Multiplayer, Coop.  
This again means that certain conflits between game modes aren't detected.  

This project attempts to provide the data necessary to detect such conflicts. Currently used in https://github.com/JaKaTaK/TheCoreConverter

# Manual Version
Updated for Sc2 patch 3.4.0.44401

Contains (mostly) every elevant command card with the 'abilityname=key' format used in .SC2Hotkeys files, structured like in the ingame hotkey editor, for a specific campaign/multiplayer/gamemode in Sc2.  
'key' in 'abilityname=key' is the default value used by that key.

Currently uses 2 abbreviations for a group of abilities that often show up in command cards:  
BasicUnitCommands: Move=M, Stop=S, MoveHoldPosition=H, MovePatrol=P, Attack=A  
PacifistUnitCommands: Move=M, Stop=S, MoveHoldPosition=H, MovePatrol=P

Scripts:  
create_command_list.py	: Creates a list of every key found in all HotkeyData files combined  
create_conflict_check.py: Creates a list of lists with every unique command card with those who are sub-variations (same but with less keys) of other removed  
verify_hotkeydata.py	: Prints out inconsistentcies in what the same ability is bound to in different HotkeyData files

# Automatic Version
A work in progress

With the Casc Viewer from http://www.zezula.net/en/casc/main.html  
You can open the SC2Data folder located at your Starcraft 2 install location i.e.: commandline 
CascView.exe "C:\Program Files (x86)\StarCraft II\SC2Data" 
From the campaigns and mods folders comes the files for the Sc2HotkeyData\data folder.

ButtonData.xml and UnitData.xml are from the base.sc2data\GameData folders.
GameHotkeys.txt are from the enus.sc2data\LocalizedData folders.

-	campaigns\liberty.sc2campaign -> libertycampaign
-	campaigns\libertystory.sc2campaign -> libertystory
-	campaigns\swarm.sc2campaign -> swarmcampaign
-	campaigns\swarmstory.sc2campaign -> swarmstory
-	campaigns\void.sc2campaign -> voidcampaign
-	campaigns\voidstory.sc2campaign -> voidstory

-	mods\alliedcommanders.sc2mod -> alliedcommanders
-	mods\core.sc2mod -> core
-	mods\liberty.sc2mod -> liberty
-	mods\libertymulti.sc2mod -> libertymulti
-	mods\missionpacks\ -> novacampaign
-	mods\novastoryassets.sc2mod -> novastoryassets
-	mods\swarm.sc2mod -> swarm
-	mods\swarmmulti.sc2mod -> swarmmulti
-	mods\void.sc2mod -> void
-	mods\voidmulti.sc2mod -> voidmulti
-	mods\voidprologue.sc2mod -> voidprologue
-	mods\challenges.sc2mod -> challenges
-	mods\war3.sc2mod -> war3
-	mods\war3data.sc2mod -> war3data
-	mods\novabasic03.sc2mod -> novabasic03
-	mods\balancemulti.sc2mod -> balancemulti
-	mods\campaigncommon.sc2mod -> campaigncommon

To get the information from arcade maps and mods open the sc2 map editor. File -> Open...
On the "Open Dcoument" Dialog go to the Tab Blizzard.
Download and open the entry you want i.e. Left 2 Die
Than click on File -> Save As... in the menu and choose .SC2Map or .SC2Mod and save it.
Open the saved file with the MPQ Editor http://zezula.net/en/mpq/download.html
Execute the MPQEditor.exe and click on open MPQ(s) and choose the file you saved with the map editor.

-	arcade Left 2 Die -> left2die

##### Some loosely used terminology:
**Button:** The squary things you can click on.  
**Ability:** What triggers when buttons are clicked.  
**Hotkey:** Keyboard/mouse keys that trigger buttons/globals. '[Hotkeys]' in .SC2Hotkeys will be refered to as globals  
**Global:** Stuff found under 'Global' in the Hotkeys edititing menu ingame.  
**Command Card:** The 5x3 grid of buttons found in the bottom-right part of the screen while playing.  

<br />
##### primary data sources
**UnitData.xml:** command cards from units. Buttons in command cards use 'Face' value to refer to ButtonData.xml.  
**ButtonData.xml:** Buttons and their properties. Uses 'Hotkey' value to refer to GameHotkeys.txt.  
**GameHotkeys.txt:** Default hotkey value used by buttons.

<br />
##### ButtonData.xml
-   CButton attribute 'id' is the one featured as ability in .SC2Hotkeys
-   attribute 'parent', all children inherit from parent
-	look for following children
	-	Universal, attribute 'value'. If value="1", UnitId isn't mentioned in the .SC2Hotkey. e.g. "Stim=" or "Stim/Marine="  
	-	HotkeySet, attribute 'value'. Buttons of the same HotkeySet does not conflict. e.g. Cloak and Decloak  
	-	Hotkey, attribute 'value'. Inherited default of this button. e.g. OracleAttack has "Button/Hotkey/Attack", same default hotkey as Attack. 
	-	HotkeyAlias, attribute 'value'. Inherited hotkey of this button. e.g. CancelCocoon has "Button/Hotkey/Attack". CancelCocoon will then ALWAYS have the same hotkey as Cancel


##### GameHotkeys.txt
-	Look for 'Button/Hotkey/...=' (hotkeys for buttons) and 'UI/Hotkey/...=' (hotkeys for globals) lines without any suffixes (_SC1, _NRS, _USD, _USDL)  
	-	e.g. 'Button/Hotkey/250mmStrikeCannons=C', then 250mmStrikeCannons/Thor=C is the .SC2Hotkeys entry.


##### UnitData.xml
-	CUnit attribue 'id' is one featured as caster in .SC2Hotkeys 
-	attribute 'parent', all children inherit from parent  
-	Children SubgroupAlias and SubgroupPriority are responsible for 'selection merging'. e.g. Sieged and Unsieged Siege Tanks
-	look for child CardLayouts and its attributes 'index', 'CardId and 'removed'. Its children  
	-   LayoutButtons and their attributes/children (they can be either)  


example of how a unit in UnitData.xml looks with default values set. '##id##' refers to the CUnit's 'id' attribute. I'm not 100% on all of these default values.

    <CUnit id="CUnit" parent="CUnit">
        <SubgroupAlias value="##id##"/>
        <SubgroupPriority value="0"/>
        <CardLayouts index="" removed="" CardId="">
            <LayoutButtons index="" Face="" Type="Undefined" Requirements="" Row="0" Column="0" removed="0"/>
  
<br />
##### some relevant ButtonData.xml children
CButton id="" parent=""
-	Element for containing buttons 
-	id: Name of the button. Referenced in .SC2Hotkeys. Reference to ingame name given by GameStrings.txt  
-	parent: this button will inherit all data in parent. Parent referenced by its id. Default: "CButton"

HotkeyAlias value=""
-	Overwrites this buttons hotkey to that of its HotkeyAlias button. Default: own button id
		
Universal value=""
-	If a button is universal, it's key in .SC2Hotkeys will not mention caster. Default: "0"  
-	e.g. 	universal: Stim= , Cancel= , overlordspeed=  not universal: Charge/Zealot= , Heal/Medivac=

HotkeySet value=""
-	Buttons with the same HotkeySet value will not conflict.

<br />	
##### some relevant UnitData.xml children  
CUnit id="" parent=""  
-	element for containing unit data. id is name of unit mentioned in .SC2Hotkeys. inherits all data from parent.

HotkeyCategory value="Unit/Category/..."
	'...' can here be values TerranUnits, TerranStory, ProtossUnits, ... , ZergStory. Otherwise the whole value is likely blank.  
	Determines where the unit shows up in the ingame hotkey interface. If value is not blank, it'll likely show up in the hotkey-editor ingame, which makes the unit's keys relevant

CardLayouts index="", CardId="", removed=""
-	houses the LayoutButtons. These are effectively command cards.

LayoutButtons Face="" Type="" Row="" Column=""
-	Element for individual buttons on a unit. Face takes on the 'CButton id' value. Type has a number of different values.

HotkeyAlias value=""
-	Unit id's in value will cause this unit to share it's ingame hotkey section with that unit  
-	e.g.	Burrowed zerg units suse their unburrowed variant as a HotkeyAlias.  
			Whenever you look at a burrow-able zerg unit in the hotkey editor,  
			the command card will then be accompanied by that of the burrowed variant.


StarCraft 2 is a game made by Blizzard Entertainment Inc, whom I am not affiliated with.
