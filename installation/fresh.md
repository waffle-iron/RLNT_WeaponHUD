# RLNT WEAPON HUD

<br>


<a name="freshinstallation"></a>

## FRESH INSTALL INSTRUCTIONS
You should only follow these install instructions if you **don't have any other of our scripts** installed!<br>
In case you have addons/scripts that start with RLNT please follow these instructions instead: [custom install instructions][custominstallation]!<br>
This instructions are noob-friendly; read everything carefully! There are many links to guide you through the right steps. Sometimes you also skip some steps to match the right order again. Don't be confused about that.


<a name="freshclient"></a>

## CLIENT INSTALLATION

1. DOWNLOAD:
	- [Download the latest version][latest]!
<br>

2. FILE-MANAGEMENT:
	- Extract the *Release.zip* and open it.
	- **In there** open the *Client* folder.
	- Now move the *rlnt* folder into your *mission.pbo*.
		>*mission.pbo* is just a placeholder. Your mission is called something like *Dayz_Epoch_11.Chernarus* depending on the map you're playing on. It's the same directory where your *init.sqf* and *description.ext* is stored in.

	- Next move the *stringTable.xml* into your *mission.pbo*.
		>The *stringTable.xml* has to be in the root of the *mission.pbo*. That's where the *init.sqf* is located. If you already have an existing *stringTable.xml* you need to merge them!

	- Check if you already have a custom *compiles.sqf* or not.
		- If you **have a custom** *compiles.sqf* follow [these steps](#customcompiles)!
		- If you **don't have a custom** *compiles.sqf* just continue reading!
<br>

3. NO CUSTOM *COMPILES.SQF*:
	- First open the **downloaded files** and move the **"Client/dayz_code/init/compiles.sqf"** into your **"mission.pbo/dayz_code/init"** folder.
	- Now open your *init.sqf* and find the following line:
		```sqf
		call compile preprocessFileLineNumbers "\z\addons\dayz_code\init\compiles.sqf";
		```
	- Paste this line **below**:
		```sqf
		call compile preprocessFileLineNumbers "dayz_code\init\compiles.sqf";
		```
	- Continue with [these steps](#init)!
<br>

<a name="customcompiles"></a>

3. CUSTOM *COMPILES.SQF*:
	- Check if you already have a custom *keyboard.sqf* or not.
		- If you **have a custom** *keyboard.sqf* follow [these steps](#customkeyboard)!
		- If you **don't have a custom** *keyboard.sqf* just continue reading!
<br>

4. NO CUSTOM *KEYBOARD.SQF*:
	- First open the **downloaded files** and move the **"Client/dayz_code/compile/keyboard.sqf"** into your **"mission.pbo/dayz_code/compile"** folder.
		>Create the directory if you don't have it yet. This simply means that you didn't use the Epoch file structure for your custom *compiles.sqf* but that's not a serious problem.

	- Now open your **custom** *compiles.sqf* and add the following line **below** the *!isDedicated* block:
		```sqf
		DZ_KeyDown_EH = compile preprocessFileLineNumbers "dayz_code\compile\keyboard.sqf";
		```
		>if you have a line that already defines the *keyboard.sqf* you are probably one of those guys that copy the whole *compiles.sqf* to their mission. If you did that you have to replace the original *keyboard.sqf* line instead!

	- Continue with [these steps](#init)!
<br>

<a name="customkeyboard"></a>

4. CUSTOM *KEYBOARD.SQF*:
	- First open your **custom** *keyboard.sqf* and find the following codeblocks:
		```sqf
		_rifle = {
			2 call dz_fn_switchWeapon;
	        _handled = true;
	    };
	    _pistol = {
			3 call dz_fn_switchWeapon;
	        _handled = true;
	    };
	    _melee = { // Also works for rifle on back if DZE_TwoPrimaries = 2;
			4 call dz_fn_switchWeapon;
	        _handled = true;
	    };
	    _throwable = { // select next non empty throwable weapon
	        if (vehicle player == player) then {
	            _ammo_throwable = [];
	            _muzzles_throwable = [];
	            _weapon_throwable = [];
	            {
	                _weapon = _x;
	                _muzzles = getArray(configFile >> "cfgWeapons" >> _weapon >> "muzzles");
	                if (count _muzzles == 0) then { _muzzles = [_weapon ]; };
	                {
	                    _muzz = _x;
	                    {
	                        if (_x in magazines player) then {
	                            _ammo_throwable set [ count _ammo_throwable, getText(configFile >> "cfgMagazines" >> _x >> "ammo") ];
	                            _muzzles_throwable set [ count _muzzles_throwable, _muzz ];
	                            _weapon_throwable set [ count _weapon_throwable, _weapon ];
	                        };                      
	                    } forEach getArray(configFile >> "cfgWeapons" >> _weapon >> _muzz >> "magazines");
	                } forEach _muzzles;
	            } forEach ["Throw"];

	            _magCount = count _ammo_throwable;
	            if (_magCount > 0) then {
	                if (isNil "KB_CurrentThrowable") then { KB_CurrentThrowable = -1; };
	                _currentAmmo = (weaponState player) select 3;       
	                _idx = _ammo_throwable find _currentAmmo;
	                if (_idx >= 0) then { KB_CurrentThrowable = _idx; };
	                KB_CurrentThrowable = (KB_CurrentThrowable + 1) mod _magCount;
	                player selectWeapon (_muzzles_throwable select KB_CurrentThrowable);
	                _handled = true;
	            };
	        };
		};
		```
	- **Replace** them with this code:
		```sqf
		_whToggle = {
			call RLNT_wh_toggleHUD;
			_handled = true;
		};
		_whPrimary = {
			2 call dz_fn_switchWeapon;
			_handled = true;
		};
		_whSecondary = {
			4 call dz_fn_switchWeapon;
			_handled = true;
		};
		_whHandgun = {
			3 call dz_fn_switchWeapon;
			_handled = true;
		};
		_whItem1 = {
			rlnt_wh_item1 call RLNT_wh_selectAction;
			_handled = true;
		};
		_whItem2 = {
			rlnt_wh_item2 call RLNT_wh_selectAction;
			_handled = true;
		};
		```
	- After you have done this find the following lines and **remove** them:
		```sqf
		[[DIK_1], _rifle] call _addArray;
		[[DIK_2], _pistol] call _addArray;
		[[DIK_3], _melee] call _addArray;
		//[[DIK_4], _throwable] call _addArray;
		```
	- Now find this line:
		```sqf
		diag_log "keyboard_keys reset";
		```
	- And paste the following code **above** it:
		```sqf
		if !(isNil "rlnt_wh_toggleKey") then {
			[rlnt_wh_toggleKey, _whToggle] call _addArray;
		};

		if (DZE_TwoPrimaries == 2) then {
			[[DIK_1], _whPrimary] call _addArray;
			[[DIK_2], _whSecondary] call _addArray;
			[[DIK_3], _whHandgun] call _addArray;
		} else {
			[[DIK_1], _whPrimary] call _addArray;
			[[DIK_2], _whHandgun] call _addArray;
			[[DIK_3], _whSecondary] call _addArray;
		};

		if (!(isNil "rlnt_wh_item1_key") && (rlnt_wh_itemAmount == 1)) then {
			[rlnt_wh_item1_key, _whItem1] call _addArray;
		};

		if (!(isNil "rlnt_wh_item1_key") && !(isNil "rlnt_wh_item2_key") && (rlnt_wh_itemAmount == 2)) then {
			[rlnt_wh_item1_key, _whItem1] call _addArray;
			[rlnt_wh_item2_key, _whItem2] call _addArray;
		};
		```
<br>

<a name="init"></a>

5. INIT:
	- Now open your *init.sqf* and paste the following line **below** the *!isDedicated* block:
		```sqf
		execVM "rlnt\addons\RLNT_init.sqf";  /*  RLNT-ADDITIONS  */
		```
<br>

6. DESCRIPTION:
	- Open your *description.ext* and paste the following line at the **very bottom**:
		```sqf
		#include "rlnt\graphics\RLNT_dialogs.hpp"  /*  RLNT-ADDITIONS  */
		```
	- Check if you have a *RscTitles* class somewhere or not!
		- If you **already have a** *RscTitles* class follow [these steps](#customrsctitles)!
		- If you **don't have a** *RscTitles* class just continue reading!
<br>

7. NO *RSCTITLES* CLASS:
	- Still in the 'description.ext', find the following line:
		```sqf
		#include "\z\addons\dayz_code\gui\description.hpp"
		```
	- **Replace** it with the this line:
		```sqf
		#include "dayz_code\gui\description.hpp"
		```
	- Now find the this codeblock:
		```sqf
		class Header
		{
			gameType = COOP;            //DM, Team, Coop, ...
			minPlayers = 1;             //min # of players the mission supports
			maxPlayers = 100;           //Max # of players the mission supports
		};
		```
	- Paste the following codeblock **underneath** it:
		```sqf
		class RscTitles {
			#include "rlnt\graphics\RLNT_displays.hpp"  /*  RLNT-ADDITIONS  */
			class wm_disp {
				idd = -1;
				onLoad = "uiNamespace setVariable ['wm_disp', _this select 0]";
				fadein = 0;
				fadeout = 0;
				duration = 10e10;
				controlsBackground[] = {};
				objects[] = {};
				class controls {
					class wm_text2 {
						idc = 1;
						x = safeZoneX+0.027;//safeZoneW*0.01;
						y = safeZoneY+safeZoneH-0.16;
						w = 0.151*safeZoneH;
						h = 0.057*safeZoneH;
						shadow = 2;
						class Attributes
						{
							font = "EtelkaNarrowMediumPro";
							color = "#24FFFFFF";
							align = "left"; // put "center" here if you want some background
							valign = "middle";
							shadow = 2;
						};
						colorBackground[] = { 1, 0.3, 0, 0 };  // uncomment and increase 4th number to have a background
						font = "EtelkaNarrowMediumPro";
						size = 0.06*safeZoneH;
						type = 13;
						style = 0;
						text="";
					};
				};
			};
		};
		```
	- Now open the **downloaded files** and move the **"Client/dayz_code/gui/description.hpp"** into your **"mission.pbo/dayz_code/gui"** folder.
	- Continue [here](#clientdone)!
<br>

<a name="customrsctitles"></a>

7. *RSCTITLES* CLASS:
	- Paste the following line **into** your *RscTitles* class:
		```sqf
		#include "rlnt\graphics\RLNT_displays.hpp"  /*  RLNT-ADDITIONS  */
		```
<br>

<a name="clientdone"></a>

**You are done with the client installation!**<br>
- Check if you have infiSTAR.
	- If you have infiSTAR [check the bottom part](#freshinfistar)!
	- If you don't have infiSTAR you are done! Now go back to the [main post][mainpost]!
<br>

<a name="freshinfistar"></a>

## INFISTAR INSTALLATION

- WHITELISTING:
	- Open your infiSTAR *AHconfig.sqf* and add the following dialog number to your *ALLOWED_Dialogs*:
		```
		7211
		```
	- **That's it!** You should now go back to the [main post][mainpost] too!

<!-- Some ASCII art to keep the guide active -->
<br><br><br><br><br><br><br><br><br><br>
```
################################################################################################################
################################################################################################################
###############++++++++++++++++++#########################################++++++++##############################
#############+++'''''''''''''''''+++++++################################++''''''''##############################
############++';;;;;;;;;;;;;;;;;;;;;''''++#############################+'';;;;;;;;##############################
############+'::,,,,,,,,,,,,,,,,,,,,:::;;''+##########################++;:,,,,,,,,##############################
###########+';:..````````````````......,,::;'#########################+';,.```````##############################
###########+';,.`                 ``````..,,:;########################+':,``     `##############################
###########+';,`                        ```.,:;#######################+':.`       ##############################
###########+';,`                           ``.,+######################+':.`       ##############################
###########+';,`                             `.,######################+':.`       ##############################
###########+';,`                              `.,#####################+':.`       ##############################
###########+';,`                               `.#####################+':.`       ##############################
###########+';,`                                `:####################+':.`       ##############################
###########+';,`       :#########:```           `.####################+':.`       ##############################
###########+';,`       :###########+.``          `####################+':.`       ##############################
###########+';,`       :############+,.``        `'###################+':.`       ##############################
###########+';,`       :#############',.``        ,++++++++++++++++###+':.`       ##############################
###########+';,`       :##############;,.`        `++++++++++++++++++#+':.`       ##############################
###########+';,`       :##############';,.`       `++++++++++++++++++#+':.`       ##############################
###########+';,`       :############+++':,`       `++++++++++++++++++#+':.`       ##############################
###########+';,`       :##########+++++';,.`      `++++++++++++++++++#+':.`       ##############################
###########+';,`       :########++++++#+;:.`       ++++++++++++++++++#+':.`       ##############################
###########+';,`       :######++++++++#+':.`       ++++++++++++++++++#+':.`       ++############################
###########+';,`       :####+++++++++##+':.`      `++++++++++++++++++#+':.`       +++###########################
###########+';,`       :###++++++++++##+;,.`      ;++++++++++++++++++#+':.`       +++++#########################
###########+';,`       :#+++++++++++##+';,`       +++++++++++++++++++#+':.`       ++++++########################
###########+';,`       :++++++++#####++;:.`       +++++++++++++++++++#+':.`       ++++++++######################
###########+';,`       `.,;'+####++++';:,.`      `+++++++++++++++++++#+':.`       +++++++++#####################
###########+';,`       `.,:''++++'''';:,.`       ++++++++++++++++++++#+':.`       ++++++++++####################
###########+';,`        `.,:;;;;;;;::,..`        ++++++++++++++++++++#+':.`       ++++++++++++##################
###########+';,`        ``.,,,,,,,,...``        +++++++++++++++++++++#+':.`       +++++++++++++#################
###########+';,`         ```.....`````         ;+++++++++++++++++++++#+':.`       ++++++++++++++################
###########+';,`            ````              `++++++++++++++++++++++#+':.`       +++++++++++++++###############
###########+';,`                             `+++++++++++++++++++++++#+':.`       ++++++++++++++++##############
###########+';,`                            :++++++++++++++++++++++++#+':.`       +++++++++++++++++#############
###########+';,`                           ++++++++++++++++++++++++++#+':.`       +++++++++++++++++#############
###########+';,`                         '+++++++++++++++++++++++++++#+':.`       ++++++++++++++++++############
###########+';,`                       ++++++++++++++++++++++++++++++#+':.`       +++++++++++++++++++###########
###########+';,`                      +++++++++++++++++++++++++++++++#+':.`       ++++++++++++++++++++##########
###########+';,`       ,+              ++++++++++++++++++++++++++++++#+':.`       ++++++++++++++++++++##########
###########+';,`       :+````          ++++++++++++++++++++++++++++++#+':.`       +++++++++++++++++++++#########
###########+';,`       :++..``         `+++++++++++++++++++++++++++++#+':.`       ++++++++++++++++++++++########
#########+#+';,`       :++:,,.`        `'++++++++++++++++++++++++++++#+':.`       ++++++++++++++++++++++########
#########+#+';,`       :+++::,`        `.++++++++++++++++++++++++++++#+':.`       +++++++++++++++++++++++#######
########++#+';,`       :+++';,.`        `'+++++++++++++++++++++++++++#+':.`       +++++++++++++++++++++++#######
########++#+';,`       :++++':,`        `.+++++++++++++++++++++++++++#+':.`       ++++++++++++++++++++++++######
#######+++#+';,`       :+++++;:.`        `;++++++++++++++++++++++++++#+':.`       ++++++++++++++++++++++++######
#######+++#+';,`       :+++++';,``       `.++++++++++++++++++++++++++#+':.`       +++++++++++++++++++++++++#####
######++++#+';,`       :++++++;:.`        `:+++++++++++++++++++++++++#+':.`       +++++++++++++++++++++++++#####
######++++#+';,`       :++++++';,.`       `.+++++++++++++++++++++++++#+':.`       ++++++++++++++++++++++++++####
#####+++++#+';,`       :+++++++;:.`        `,++++++++++++++++++++++++#+':.`       ++++++++++++++++++++++++++####
#####+++++#+';,`       :+++++++';,.`       `.++++++++++++++++++++++++#+':.`       +++++++++++++++++++++++++++###
#####+++++#+';,`       :++++++++':.`        `,+++++++++++++++++++++++#+':.`       +++++++++++++++++++++++++++###
####++++++#+';,`       :++++++++';,.`       `.+++++++++++++++++++++++#+':.`       +++++++++++++++++++++++++++###
####++++++#+';,`       :+++++++++':.`        `.++++++++++++++++++++++#+':.`       ++++++++++++++++++++++++++++##
####++++++#+';,`       :+++++++++';,.`       `.++++++++++++++++++++++#+':.`       ++++++++++++++++++++++++++++##
###+++++++#+';,`       :++++++++++':.`        `.+++++++++++++++++++++#+':.`       `.:;++#############+++++++++##
###+++++++#+';,`       :++++++++++';,.`       `.+++++++++++++++++++++#+':.`       `.,;''++++++++++++++++++++++##
###+++++++#+';,`       :+++++++++++':,`        `.++++++++++++++++++++#+':.`       ``.:;;;;;;;;;;;;;;;++++++++++#
###+++++++#+';,`       :+++++++++++';,.`       `.'+++++++++++++++++++#+':.`        `..,,,,,,,,,,,,,,:++++++++++#
###+++++++#+';,`       :++++++++++++':,`        `.+++++++++++++++++++#+':.`         ```..............++++++++++#
##++++++++#+';,`       :+++++++++++++;,.`       ``'++++++++++++++++++#+':.`            ``````````````++++++++++#
##++++++++#+';,`       :+++++++++++++':,`        `.++++++++++++++++++#+':.`                          ++++++++++#
##++++++++#+';,`       :++++++++++++++;:.`        `;+++++++++++++++++#+':.`                          +++++++++++
##++++++++#+';,`       :++++++++++++++':,`        `.+++++++++++++++++#+':.`                          +++++++++++
##++++++++#+';,`       :+++++++++++++++;:.`        `:++++++++++++++++#+':.`                          +++++++++++
##++++++++#+';,`       :+++++++++++++++';,``       `.++++++++++++++++#+':.`                          +++++++++++
##++++++++#+';,`       :++++++++++++++++;:.`       ``,+++++++++++++++#+':.`                          +++++++++++
##++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
#############++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++###
#############################+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++##########
##########+++++++++++###########+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++#############
#########';.````````````@########++++++++++';,``````````#########+++;'```````````````````````````````@##########
#########';.```````````` @########+++++++++';,``````````###########+;'```````````````````````````````@##########
#########';.```````````` +@########++++++++';,``````````###########+;'```````````````````````````````@##########
#########';.````````````  @@#######++++++++';,``````````###########+;'```````````````````````````````@##########
#########';.````````````` `@#########++++++';,``````````###########+''```````````````````````````````@##########
#########';``````````````  #@#########+++++'',``````````@##########+''```````````````````````````````@##########
#########';```````````````  @@########+#+++'',``````````@##########+'+```````````````````````````````@##########
#########';```````````````  `@#############'',``````````@@#########+++##########``````````:@@@@@@@@@@###########
#########';````````````````` #@############';,``````````@@#########++##########+``````````:@@@@@@@@@############
#########';```````````````````@@###########';,``````````@@#########+#########+''``````````:@####################
#########';```````````````````.@###########';,``````````@@##########+########+;;``````````:@####################
#########';````````````````````#@##########';,``````````@@#########+##+######+;;``````````:@####################
#########';`````````````````````@@#########+;,``````````@@##########++++#####+;;``````````:@####################
#########';`````````````````````,@#########+;,``````````@@##########+++++####+;;``````````:@####################
#########';``````````````````````@@########+;,``````````@@#########++++++####+;'``````````:@####################
#########';```````````````````````@@#######+;,``````````@@#########+++++++###+;'``````````:@####################
#########';```````````:```````````:@#######+;,``````````@@#########+++++++###+;'``````````:@####################
#########';```````````@````````````@@######+;,``````````@@##########++++++###+;'``````````:@####################
#########';```````````@#````````````@@#####+;,``````````@@#########+++++++###+;'``````````:@####################
#########';```````````@#,```````````;@#####+;,``````````@@#########++++++++##+;'``````````:@####################
#########';```````````@@#````````````@@####+;,``````````@@#########++++++++##+;'``````````:@####################
#########';```````````@@##````````````@@###+;,``````````@@##########+++++++##+;'``````````:@####################
#########';```````````@@##.```````````'@###+;,``````````@@##########++++++###+;'``````````:@####################
#########';```````````@@#+#````````````@@##+;,``````````@@###########++++++##+;'``````````:@####################
#########';```````````@@#+#+````````````@@#+;,``````````@@###########+++++###+;'``````````:@####################
#########';```````````@@##+#.```````````+@#';,``````````@@###########++++####+;'``````````:@####################
#########';```````````@@##++#````````````@@';,``````````@@###########+#++####+;'``````````:@####################
#########';```````````@@###+++````````````@#;,``````````@@#############++####+;;``````````:@####################
#########';```````````@@####+#````````````#@;,``````````@@#############+#####+;;``````````:@####################
#########';```````````@@####++#````````````@',``````````@@###################+;;``````````:@####################
#########';```````````@@#####+++````````````#.``````````@@###################+;;``````````:@####################
#########';```````````@@######++````````````#,``````````@@###################+;;``````````:@####################
#########';```````````@@######+++````````````,``````````@@###################+;;``````````:@####################
#########';```````````@@#######++'``````````````````````@@###################+;;``````````:@####################
#########';```````````@@#######+++``````````````````````@@###################+;;``````````:@####################
#########+;```````````@@########+++`````````````````````@@###################+;;``````````:@####################
#########+;```````````@@#########++;````````````````````@@###################+;;``````````:@####################
#########+;```````````@@#########+++````````````````````@@###################+;;``````````:@####################
#########+;```````````@@##########+++```````````````````@@###################+;;``````````:@####################
#########+;```````````@@###########++:``````````````````@@###################+;;``````````:@####################
#########+;```````````@@###########+++  ````````````````@@###################+;;``````````:@####################
#########+;```````````@@############+++  ```````````````@@###################+;;``````````:@####################
#########+;```````````@@############+++,  ``````````````@@###################+;;``````````:@####################
#########+;```````````@@#############+++  ``````````````@@###################+;;``````````:@####################
#########+;```````````@@##############+++  `````````````@@###################+;;``````````:@####################
#########+;```````````@@##############+++. `````````````@@###################+;;``````````:@####################
#########+;```````````@@###############+++  ````````````@@###################+;;``````````:@####################
#########+;```````````@@################+++ ````````````@@###################+;;``````````:@####################
#########+;```````````@@################+++.````````````@@###################+;;``````````:@####################
#########+;```````````@@#################+++````````````@@###################+;;``````````:@####################
#########+'.``````````@@##################+++```````````@@###################+''``````````:@####################
#########+'.``````````@@##################++#```````````@@###################+'+``````````:@####################
#########++##@@@@@@@@@@####################+###@@@@@@@@@@#####################+###@@@@@@@@@@####################
#############@@@@@@@@@#########################@@@@@@@@@######################+###@@@@@@@@@#####################
################################################################################################################
```


<!-- Links -->
[custominstallation]: https://github.com/RLNT/RLNT_WeaponHUD/blob/master/installation/custom.md "Go to source"
[latest]: https://github.com/RLNT/RLNT_WeaponHUD/releases "Go to source"
[mainpost]: https://github.com/RLNT/RLNT_WeaponHUD#config "Go to source"
