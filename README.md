# 1#include <amxmodx>
#include <dhudmessage>
#include <cstrike>
#include <colorchat>
#include <hamsandwich>
#include <engine>
#include <fun>
#include <sqlx>
#include <fakemeta>

new const PLUGIN[] = "Fun_Mod";
new const VERSION[] = "1.1.2";
new const AUTHOR[] = "HaTe, Deadpool, ~BoNe"; //Ha nem vagy pofátlan nem írod át örűlj, hogy megoszottam a modot!

#pragma semicolon 1
//---------------------------------------------------------------------------------------
//Beállítások
//---------------------------------------------------------------------------------------

new const Prefix[] = "[.::*[Dragon*.*KinG]*::.]"; //Menüben megjelenő prefix
new const C_Prefix[] = "[Információ]"; //Chat Prefix

new const Website[] = "facebook.com/groups/LighTTeam/"; //Menükben megjelenő elérhetőség

new const SQLINFO[][] = { "127.0.0.1", "root", "", "tesztszerver" };

#define MAX 114 //Skinek száma
#define LADA 6 //Ládák száma

#define TULAJ ADMIN_IMMUNITY //a
#define FOADMIN ADMIN_LEVEL_B
#define ADMIN ADMIN_BAN
#define VIP ADMIN_LEVEL_H

#define DLMIN 1 //Minimum Dollár drop
#define DLMAX 6 //Maximum Dollár drop

#define VIPELET 15 //VIP-nek járó + élet

#define KESDROP 0.5 //Kés drop esélye

new const szabaly[][] = { "Tilos a szerveren a segédprogramok használata", "Tilos az adminok szidása", "Tilos a játékostársaid anyázni" };
new const l_Nevek[][] = { "Kezdő Láda", "Vas Láda", "Gyémánt Láda", "Arany Láda", "Gamma Láda", "VIP Láda" };
new const KulcsNevek[][] = { "Kezdő Kulcs", "Vas Kulcs", "Gyémánt Kulcs", "Arany Kulcs", "Gamma Kulcs", "VIP Kulcs" };

//---------------------------------------------------------------------------------------
//---------------------------------------------------------------------------------------
//---------------------------------------------------------------------------------------

new OsszesSkin[MAX][33], Kulcs[33], Rang[33], Oles[33], Skin[11][33], bool:Gun[33], bool:Hud[33], D_Oles[33], name[32][33], Masodpercek[33], SMS[33], Vip[33], Erteke[33], kicucc[33], kirakva[33], AutoB[33], pido;
new bool:Belepve[33], bool:Beirtjelszot[33], bool:Beirtjelszot1[33], bool:Beirtfelhasznalot[33], bool:Beirtfelhasznalot1[33], Regisztralt[33], Felhasznalonev[33][100], Jelszo[33][100], regJelszo[33][100], regFh[33][100], Send[33], TempID;
new Handle:g_SqlTuple;
new Temp[192];
static color[10];
enum _:Rangs { Szint[32], Xp[8] };


//Pénz/Szint//
new Float:EXP[33];
new Float:Dollar[33];

//Kulcs/Láda//
new Lada[LADA][33];
new LadaK[LADA][33];

//Szerencse/Játékok//
new Berakepiros[33], Berakezold[33], Berakeszurke[33];

new const Fegyverek[MAX][] =
{
	{ "Anubis AK47" },
	{ "Aquamarine_Revenge AK47" },
	{ "Astronaut AK47" },
	{ "Black_and_Green AK47" },
	{ "Bloodsport AK47" },
	{ "Blue_Lines AK47" },
	{ "BooM AK47" },
	{ "SuperBoy_Lore AK47" },
	{ "Elite_Build AK47*" },
	{ "frontside_misty AK47" },
	{ "Furious_Peacock AK47" },
	{ "Gold AK47" },
	{ "Graffiti AK47" },
	{ "Horas AK47" },
	{ "Illusion AK47" },
	{ "Jaguar AK47" }, 
	{ "MARIHUANA AK47" }, 
	{ "Neon_Revolution AK47" }, 
	{ "ORION AK47" }, 
	{ "Propaganda AK47" }, 
	{ "Redline AK47" }, 
	{ "Skull AK47" }, //22 AK47
	
	
	{ "Acho M4A1" },
	{ "Asiimov M4A1" },
	{ "Black M4A1" },
	{ "BulletRain M4A1" },
	{ "Cool M4A1" },
	{ "Crome M4A1" },
	{ "Desert M4A1" },
	{ "Dragon M4A1" },
	{ "DragonKing M4A1" },
	{ "Fain M4A1" },
	{ "Fotote M4A1" },
	{ "Future M4A1" },
	{ "Galaxy M4A1" },
	{ "Griffin M4A1" },
	{ "Howl M4A1" },
	{ "basilisk M4A1" },
	{ "battlestar M4A1" },
	{ "buzzkill M4A1" },
         { "chanticosfire M4A1" },
	{ "cybernetik M4A1" },
	{ "cyrex M4A1" },
	{ "decimator M4A1" },
         { "desolatespace M4A1" },
	
	
	{ "Aranytekercs AWP" },
	{ "Babylon AWP" },
	{ "BOOM AWP" },
	{ "Captain_Strike AWP" },
	{ "comic AWP" },
	{ "De_Jackal AWP" },
	{ "Death AWP" },
	{ "Deimos AWP" },
	{ "Disco_Party AWP" },
	{ "SuperBoy_Lore AWP" },
	{ "Electic_Hive AWP" },
	{ "Glasgow_Beast AWP" },
	{ "Ice_Palm AWP" },
	{ "Leviathan_Kiss AWP" },
	{ "Lightning_strike AWP" }, 
	{ "Machine AWP" }, 
	{ "Medusa AWP" }, 
	{ "PAW AWP" }, 
	{ "Plasmax AWP" }, 
	{ "Primal AWP" }, 
	{ "RedLine AWP" }, 
	{ "Ronin AWP" }, 
	{ "Tiger AWP" }, 
	{ "Unicorn AWP" }, 
	{ "virus AWP" }, //25 AWP
	
	
	{ "Bach Deagle" },
	{ "Black_and_Red Deagle" },
	{ "BlackCountrains Deagle" },
	{ "Cobalt_Disruption Deagle" },
	{ "CoLoR_DeAgLe Deagle" },
	{ "Cyberwanderer_Black Deagle" },
	{ "d1 Deagle*" },
	{ "d2 Deagle" },
	{ "Extreme Deagle" },
	{ "Flames Deagle" },
	{ "Frankenstein Deagle" },
	{ "Glorius Deagle" },
	{ "glory Deagle" },
	{ "Gold Deagle" },
	{ "Graan Deagle" },
	{ "Hypnotic Deagle" },
	{ "Ice_SuperBoy Deagle" },
	{ "Jupiters_Mist Deagle" },
	{ "Kumicho_SuperBoy Deagle" },
	{ "OakleyDeagle Deagle" },
	{ "Orochi Deagle" },
	{ "Oxide_Blaze Deagle" },
	{ "Plasmax Deagle" },
	{ "Standard Deagle" }, //24 DEAGLE
	
	
	{ "banan Kés" },
	{ "d_dopplerphase Kés" },
	{ "d_gamma Kés" },
	{ "d_griff Kés" },
	{ "d_howl Kés" },
	{ "d_lore Kés" },
	{ "d_rednature Kés" },
	{ "hd_baydamascus Kés" },
	{ "hd_baySuperBoysoul Kés" },
	{ "hd_bayneo Kés" }, 
	{ "hd_baytiger Kés" }, 
	{ "hd_baywater  Kés" }, 
	{ "hd_butsong Kés" }, 
	{ "hd_flipd Kés" }, 
	{ "hd_karbloode kés" }, 
	{ "hd_karbloss Kés" }, 
	{ "hd_kardopshap Kés" }, 
	{ "hd_karfade Kés" }, 
	{ "hd_karsuperfurry Kés" }, 
	{ "Warhamer Kés" } //20 KNIFE
};
new const Ak47_Modelek[][] = {
	"models/v_ak47.mdl",
     "models/DragonKing/ak47/anubis.mdl",
	"models/DragonKing/ak47/aquamarine_revenge.mdl",
	"models/DragonKing/ak47/astronaut.mdl",
	"models/DragonKing/ak47/black_and_green.mdl",
	"models/DragonKing/ak47/bloodsport.mdl",
	"models/DragonKing/ak47/blue_lines.mdl",
	"models/DragonKing/ak47/boom.mdl",
	"models/DragonKing/ak47/Dragon_lore.mdl",
	"models/DragonKing/ak47/elite_build.mdl",
	"models/DragonKing/ak47/frontside_misty.mdl",
	"models/DragonKing/ak47/furious_peacock.mdl",
	"models/DragonKing/ak47/gold.mdl",
	"models/DragonKing/ak47/graffiti.mdl",
	"models/DragonKing/ak47/horas.mdl",
	"models/DragonKing/ak47/illusion.mdl",
	"models/DragonKing/ak47/jaguar.mdl",
	"models/DragonKing/ak47/marihuana.mdl",
	"models/DragonKing/ak47/neon_revolution.mdl",
	"models/DragonKing/ak47/orion.mdl",
	"models/DragonKing/ak47/propaganda.mdl",
	"models/DragonKing/ak47/redline.mdl",
	"models/DragonKing/ak47/skull.mdl" //22 AK
};
new const M4a1_Modelek[][] = {
	"models/v_m4a1.mdl",
	"models/DragonKing/m4a1/Acho.mdl",
	"models/DragonKing/m4a1/Asiimov.mdl",
	"models/DragonKing/m4a1/Black.mdl",
	"models/DragonKing/m4a1/BulletRain.mdl",
	"models/DragonKing/m4a1/Cool.mdl",
	"models/DragonKing/m4a1/Crome.mdl",
	"models/DragonKing/m4a1/Desert.mdl",
	"models/DragonKing/m4a1/Dragon.mdl",
	"models/DragonKing/m4a1/DragonKing.mdl",
	"models/DragonKing/m4a1/Fain.mdl",
	"models/DragonKing/m4a1/Fotote.mdl",
	"models/DragonKing/m4a1/Future.mdl",
	"models/DragonKing/m4a1/Galaxy.mdl",
	"models/DragonKing/m4a1/Griffin.mdl",
	"models/DragonKing/m4a1/Howl.mdl",
	"models/DragonKing/m4a1/m4a1_basilisk.mdl",
	"models/DragonKing/m4a1/m4a1_battlestar.mdl",
	"models/DragonKing/m4a1/m4a1_buzzkill.mdl",
	"models/DragonKing/m4a1/m4a1_chanticosfire.mdl",
	"models/DragonKing/m4a1/m4a1_cybernetik.mdl",
	"models/DragonKing/m4a1/m4a1_cyrex.mdl",
	"models/DragonKing/m4a1/m4a1_decimator.mdl",
	"models/DragonKing/m4a1/m4a1_desolatespace.mdl"
};
new const Awp_Modelek[][] = {
	"models/v_awp.mdl",
	"models/DragonKing/awp/aranytekercs.mdl",
	"models/DragonKing/awp/babylon.mdl",
	"models/DragonKing/awp/boom.mdl",
	"models/DragonKing/awp/captain_strike.mdl",
	"models/DragonKing/awp/comic.mdl",
	"models/DragonKing/awp/de_jackal.mdl",
	"models/DragonKing/awp/death.mdl",
	"models/DragonKing/awp/deimos.mdl",
	"models/DragonKing/awp/disco_party.mdl",
	"models/DragonKing/awp/Dragon_lore.mdl",
	"models/DragonKing/awp/electic_hive.mdl",
	"models/DragonKing/awp/glasgow_beast.mdl",
	"models/DragonKing/awp/ice_palm.mdl",
	"models/DragonKing/awp/ieviathan_kiss.mdl",
	"models/DragonKing/awp/lightning_strike.mdl",
	"models/DragonKing/awp/machine.mdl",
	"models/DragonKing/awp/medusa.mdl",
	"models/DragonKing/awp/paw.mdl",
	"models/DragonKing/awp/plasmax.mdl",
	"models/DragonKing/awp/primal.mdl",
	"models/DragonKing/awp/redline.mdl",
	"models/DragonKing/awp/ronin.mdl",
	"models/DragonKing/awp/tiger.mdl",
	"models/DragonKing/awp/unicorn.mdl",
	"models/DragonKing/awp/virus.mdl" //25 AWP
};
new const Deagle_Modelek[][] = {
	"models/v_deagle.mdl",
	"models/DragonKing/deagle/bach.mdl",
	"models/DragonKing/deagle/black_and_red.mdl",
	"models/DragonKing/deagle/blackcountrains.mdl",
	"models/DragonKing/deagle/cobalt_disruption.mdl",
	"models/DragonKing/deagle/color_deagle.mdl",
	"models/DragonKing/deagle/cyberwanderer_black.mdl",
	"models/DragonKing/deagle/d1.mdl",
	"models/DragonKing/deagle/d2.mdl",
	"models/DragonKing/deagle/extreme.mdl",
	"models/DragonKing/deagle/flames.mdl",
	"models/DragonKing/deagle/frankenstein.mdl",
	"models/DragonKing/deagle/glorius.mdl",
	"models/DragonKing/deagle/glory.mdl",
	"models/DragonKing/deagle/gold.mdl",
	"models/DragonKing/deagle/graan.mdl",
	"models/DragonKing/deagle/hypnotic.mdl",
	"models/DragonKing/deagle/ice_DragonKing.mdl",
	"models/DragonKing/deagle/jupiters_mist.mdl",
	"models/DragonKing/deagle/kumicho_lore.mdl",
	"models/DragonKing/deagle/oakley_deagle.mdl",
	"models/DragonKing/deagle/orochi.mdl",
	"models/DragonKing/deagle/oxide_blaze.mdl",
	"models/DragonKing/deagle/plasmax.mdl",
	"models/DragonKing/deagle/standard.mdl" //24 Deagle
};
new const Kes_Modelek[][] = {
	"models/v_knife.mdl",
	"models/DragonKing/knife/banan.mdl",
	"models/DragonKing/knife/d_dopplerphase.mdl",
	"models/DragonKing/knife/d_gamma.mdl",
	"models/DragonKing/knife/d_griff.mdl",
	"models/DragonKing/knife/d_howl.mdl",
	"models/DragonKing/knife/d_lore.mdl",
	"models/DragonKing/knife/d_rednature.mdl",
	"models/DragonKing/knife/hd_baydamascus.mdl",
	"models/DragonKing/knife/hd_baysoul.mdl",
	"models/DragonKing/knife/hd_bayneo.mdl",
	"models/DragonKing/knife/hd_baytiger.mdl",
	"models/DragonKing/knife/hd_baywater.mdl",
	"models/DragonKing/knife/hd_butsong.mdl",
	"models/DragonKing/knife/hd_flipd.mdl",
	"models/DragonKing/knife/hd_karbloode.mdl",
	"models/DragonKing/knife/hd_karbloss.mdl",
	"models/DragonKing/knife/hd_kardopshap.mdl",
	"models/DragonKing/knife/hd_karfade.mdl",
	"models/DragonKing/knife/hd_karsuperfurry.mdl",
	"models/DragonKing/knife/Warhamer.mdl" //20 Kés
};
new const Rangok[][Rangs] =
{
	{ "LighT Tag I", 25 },
	{ "LighT Tag II", 100 },
	{ "LighT Tag III", 250 },
	{ "LighT Tag IV", 500 },
	{ "LighT Elite", 700 },
	{ "LighT Elite Master", 850 },
	{ "LighT Nova I", 1000 },
	{ "LighT Nova II", 4000 },
	{ "LighT Nova III", 7000 },
	{ "LighT Nova Master", 7800 },
	{ "LighT Guardian I", 8500 },
	{ "LighT Guardian II", 9999 },
	{ "LighT Guardian Elite", 10500 },
	{ "LighT Ak47 Master", 12000 },
	{ "LighT Minden napos Jatekos", 14000 },
	{ "LighT Jatekos ki egeto", 16000 },
	{ "LighT Admin egeto", 18000 },
	{ "LighT Szerver egeto jatekos", 20000 }
};
public plugin_init() 
{
	register_plugin(PLUGIN, VERSION, AUTHOR);
	register_clcmd("say /add", "addolas");
	register_impulse(201, "Ellenorzes");
	register_clcmd("DOLLAR", "lekeres");
	register_clcmd("say", "sayhook");
	
	register_clcmd("Reg_Felhasznalonev", "regisztralas_felh");
	register_clcmd("Reg_Jelszo", "regisztralas_jelszo");
	register_clcmd("Log_Felhasznalonev", "bejelentkezes_felh");
	register_clcmd("Log_Jelszo", "bejelentkezes_jelszo");
	register_clcmd("KMENNYISEG", "ObjectSend");
	
	RegisterHam(Ham_Spawn, "player", "VipEllenorzes", 1);
	
	register_event("CurWeapon", "FegyverValtas", "be", "1=1");
	register_event("DeathMsg", "Halal", "a");
	
	set_task(1.0, "AutoCheck",_,_,_,"b");
	
	register_clcmd("BET", "rulilekeres"); //betet
	register_clcmd("BET1", "rulilekeres1"); //betet 1
	register_clcmd("BET2", "rulilekeres2"); //betet 2
}
public addolas(id)
{
	if(get_user_flags(id) & ADMIN_IMMUNITY)
	{
		for(new i;i < MAX; i++)
			OsszesSkin[i][id] += 1;
			
		for(new i;i < LADA; i++)
			Lada[i][id] += 10;
			
			for(new i;i < LADA; i++)
			LadaK[i][id] += 10;
			
		SMS[id] += 2032;
		ColorChat(id, GREEN, "%s  ^1Sikeresen mindent be addoltál!", C_Prefix);
	}
	else
	{
	          ColorChat(id, GREEN, "%s ^1Nincs jogod ehhez!", C_Prefix);
	}
}
public AutoCheck()
{
new p[32],n;
get_players(p,n,"ch");
for(new i=0;i<n;i++)
{
new id = p[i];
if(Hud[id])
{
	InfoHud(id);
}
}
}
public VipEllenorzes(id)
{
if(Vip[id] >= 1) set_user_flags(id, get_user_flags(id) | VIP);
if(get_user_flags(id) & VIP)
{
	set_user_health(id, get_user_health(id) + VIPELET);
	Fegyvermenu(id);
}
}
public InfoHud(id)
{
	new Target = pev(id, pev_iuser1) == 4 ? pev(id, pev_iuser2) : id;
	
	if(is_user_alive(id))
	{
		new iMasodperc, iPerc, iOra, Nev[32];
		get_user_name(id, Nev, 31);
		iMasodperc = Masodpercek[id] + get_user_time(id);
		iPerc = iMasodperc / 60;
		iOra = iPerc / 60;
		iMasodperc = iMasodperc - iPerc * 60;
		iPerc = iPerc - iOra * 60;
		
		set_hudmessage(random(255), random(255), random(255), 0.01, 0.15, 0, 6.0, 1.1, 0.0, 0.0, -1);
		show_hudmessage(id, "Üdv a szerveren!^n Név: %s^nTapasztalat: %3.2f^nDollár: %.2f^nSMS Pont: %d^nJátszott idő: %d óra %d perc %d mp^n^n%s", Nev, EXP[id], Dollar[id], SMS[id], iOra, iPerc, iMasodperc, Website);
	}
	else
	{
		new iMasodperc, iPerc, iOra;
		iMasodperc = Masodpercek[Target] + get_user_time(Target);
		iPerc = iMasodperc / 60;
		iOra = iPerc / 60;
		iMasodperc = iMasodperc - iPerc * 60;
		iPerc = iPerc - iOra * 60;
		
		set_hudmessage(random(255), random(255), random(255), 0.01, 0.15, 0, 6.0, 1.1, 0.0, 0.0, -1);
		show_hudmessage(id, "Nézett játékos adatai^nTapasztalat: %3.2f^nDollár: %.2f^nSMS Pont: %d^nJátszott idő: %d óra %d perc %d mp^n^n%s", EXP[Target], Dollar[Target], SMS[Target], iOra, iPerc, iMasodperc, Website);
		
		set_hudmessage(0, 255, 0, 0.66, 0.96, 0, 6.0, 1.0);
		show_hudmessage(id, "Verzió: %s", VERSION);
	}
}
public plugin_precache()
{
for(new i;i < sizeof(Ak47_Modelek); i++)
{
	precache_model(Ak47_Modelek[i]);
}
for(new i;i < sizeof(M4a1_Modelek); i++)
{
	precache_model(M4a1_Modelek[i]);
}
for(new i;i < sizeof(Awp_Modelek); i++)
{
	precache_model(Awp_Modelek[i]);
}
for(new i;i < sizeof(Deagle_Modelek); i++)
{
	precache_model(Deagle_Modelek[i]);
}
for(new i;i < sizeof(Kes_Modelek); i++)
{
	precache_model(Kes_Modelek[i]);
}
precache_model("models/v_knife.mdl");
}
public FegyverValtas(id)
{
new fgy = get_user_weapon(id);

for(new i;i < sizeof(Ak47_Modelek); i++)
{
	if(Skin[0][id] == i && fgy == CSW_AK47 && Gun[id])
	{
		set_pev(id, pev_viewmodel2, Ak47_Modelek[i]);
	}
}
for(new i;i < sizeof(M4a1_Modelek); i++)
{
	if(Skin[1][id] == i && fgy == CSW_M4A1 && Gun[id])
	{
		set_pev(id, pev_viewmodel2, M4a1_Modelek[i]);
	}
}
for(new i;i < sizeof(Awp_Modelek); i++)
{
	if(Skin[2][id] == i && fgy == CSW_AWP && Gun[id])
	{
		set_pev(id, pev_viewmodel2, Awp_Modelek[i]);
	}
}
for(new i;i < sizeof(Deagle_Modelek); i++)
{
	if(Skin[3][id] == i && fgy == CSW_DEAGLE && Gun[id])
	{
		set_pev(id, pev_viewmodel2, Deagle_Modelek[i]);
	}
}
for(new i;i < sizeof(Kes_Modelek); i++)
{
	if(Skin[10][id] == i && fgy == CSW_KNIFE && Gun[id])
	{
		set_pev(id, pev_viewmodel2, Kes_Modelek[i]);
	}
	if(Skin[10][id] == 0 && fgy == CSW_KNIFE && Gun[id] && cs_get_user_team(id) == CS_TEAM_CT)
	{
		set_pev(id, pev_viewmodel2, "models/v_knife.mdl");
	}
}
}
public Halal()
{
new Gyilkos = read_data(1);
new Aldozat = read_data(2);
new Float:pPont = random_float(0.1,0.32);
new Float:EXPKap = random_float(0.1,0.12); 

if(Gyilkos == Aldozat)
    return PLUGIN_HANDLED;
	
Oles[Gyilkos] ++;
D_Oles[Gyilkos] ++;
	
while(Oles[Gyilkos] >= Rangok[Rang[Gyilkos]][Xp])
Rang[Gyilkos]++;
	
Dollar[Gyilkos] += pPont;
EXP[Gyilkos] += EXPKap; 

set_hudmessage(random(255), random(255), random(255), 0.01, 0.15, 0, 6.0, 1.1, 0.0, 0.0, -1);
show_dhudmessage(Gyilkos, "+ %3.2f TP", EXPKap);
client_print_color(Gyilkos, print_team_default, "^4%s ^1mivel megöltél egy embert kaptál ^3%.2f ^1Dollárt!", C_Prefix, pPont);
   
LadaDropEllenor(Gyilkos);
return PLUGIN_HANDLED;

}
public LadaDropEllenor(id)
{
new LadaID = random_num(0,LADA);
new LadaKID = random_num(0,LADA);

if(D_Oles[id] == 6)
{
	Lada[LadaID][id]++;
	ColorChat(id, GREEN, "%s ^1Találtál egy ^4%s^1-t", C_Prefix, l_Nevek[LadaID]);
}
if(D_Oles[id] >= 11)
{
	LadaK[LadaKID][id]++;
	D_Oles[id] = 0;
	ColorChat(id, GREEN, "%s ^1Találtál egy ^4%s^1-t", C_Prefix, KulcsNevek[LadaKID]);
}
}
public ObjectSend(id)
{
new Data[121];
new SendName[32], TempName[32];

read_args(Data, charsmax(Data));
remove_quotes(Data);
get_user_name(id, SendName, 31);
get_user_name(TempID, TempName, 31);

if(str_to_num(Data) < 1) 
	return PLUGIN_HANDLED;

if(Send[id] == 1 && Dollar[id] >= str_to_num(Data))
{
	Dollar[TempID] += str_to_num(Data);
	Dollar[id] -= str_to_num(Data);
	ColorChat(0, GREEN, "%s^3%s ^1Küldött ^4%d Dollár^1-t ^3%s^1-nak", C_Prefix, SendName, str_to_num(Data), TempName);
}
if(Send[id] == 2 && Kulcs[id] >= str_to_num(Data))
{
	Kulcs[TempID] += str_to_num(Data);
	Kulcs[id] -= str_to_num(Data);
	ColorChat(0, GREEN, "%s^3%s ^1Küldött ^4%d Kulcs^1-t ^3%s^1-nak", C_Prefix, SendName, str_to_num(Data), TempName);
}
if(Send[id] == 3 && SMS[id] >= str_to_num(Data))
{
	SMS[TempID] += str_to_num(Data);
	SMS[id] -= str_to_num(Data);
	ColorChat(0, GREEN, "%s^3%s ^1Küldött ^4%d SMS Pont^1-ot ^3%s^1-nak", C_Prefix, SendName, str_to_num(Data), TempName);
}
for(new i;i < LADA; i++) 
{
	if(Send[id] == i + 4 && Lada[i][id] >= str_to_num(Data))
	{
		Lada[i][TempID] += str_to_num(Data);
		Lada[i][id] -= str_to_num(Data);
		ColorChat(0, GREEN, "%s^3%s ^1Küldött ^4%d %s^1-t ^3%s^1-nak", C_Prefix, SendName, str_to_num(Data), l_Nevek[i], TempName);
	}
}

return PLUGIN_HANDLED;
}
public Ellenorzes(id)
{
if(AutoB[id] == 1)
{
	Belepve[id] = true;
}
if(Belepve[id] == false)
{
	Menu_Fo(id);
}
else
{
	Fomenu(id);
}
}
new const REGMENU[][][] = { { "\w[Státusz]: \dNem Regisztrált", "\w[Státusz]: \dKijelentkezve" } };
public Menu_Regisztracio(id) 
{
	new String[121], Nev[32];
	get_user_name(id, Nev, 31);
	formatex(String, charsmax(String), "%s \r- \dRegisztráció^n%s", Prefix, REGMENU[0][Regisztralt[id]]);
	new menu = menu_create(String, "Menu_Regisztracio_h");
	
	if(Regisztralt[id] == 0)
	{
	formatex(String, charsmax(String), "\wFelhasználónév: \r%s^n", regFh[id]);
	menu_additem(menu, String, "1",0);
	formatex(String, charsmax(String), "\wJelszó: \r%s^n", regJelszo[id]);
	menu_additem(menu, String, "2",0);
	}
	else
	{
	formatex(String, charsmax(String), "\rNév: \d%s^n\wTe már regisztráltál a szerverre.", Nev, regFh[id]);
	menu_additem(menu, String, "",0);
	}
	
	if(Beirtfelhasznalot[id] == true && Beirtjelszot[id] == true)
	{
	formatex(String, charsmax(String), "\yRegisztráció");
	menu_additem(menu, String, "3",0);
	}
	
	menu_display(id, menu, 0);
	return PLUGIN_HANDLED;
}
public Menu_Regisztracio_h(id, menu, item)
{
if(item == MENU_EXIT)
{
menu_destroy(menu);
return;
}

new data[9], szName[64];
new access, callback;
menu_item_getinfo(menu, item, access, data,charsmax(data), szName,charsmax(szName), callback);
new key = str_to_num(data);

switch(key) 
{
	case 1:
	{
		client_cmd(id, "messagemode Reg_Felhasznalonev");
	}
	case 2:
	{
		client_cmd(id, "messagemode Reg_Jelszo");
	}
	case 3:
	{
		Regisztralt[id] = 1;
		ColorChat(id, GREEN, "%s^1Sikeresen regisztráltál a szerverre.", C_Prefix);
		SQL_Update_Reg(id);
	}
}
}
public Menu_Bejelentkezes(id) 
{
	new String[121];
	formatex(String, charsmax(String), "%s \r- \dBejelentkezés^n%s", Prefix, REGMENU[0][Regisztralt[id]]);
	new menu = menu_create(String, "Menu_Bejelentkezes_h");
	
	formatex(String, charsmax(String), "Felhasználónév: \r%s^n", Felhasznalonev[id]);
	menu_additem(menu, String, "1",0);
	formatex(String, charsmax(String), "Jelszó: \r%s^n", Jelszo[id]);
	menu_additem(menu, String, "2",0);
	
	formatex(String, charsmax(String), "\yBejelentkezés^n");
	menu_additem(menu, String, "3",0);
	
	menu_display(id, menu, 0);
	return PLUGIN_HANDLED;
}
public Menu_Bejelentkezes_h(id, menu, item)
{
if(item == MENU_EXIT)
{
menu_destroy(menu);
return;
}

new data[9], szName[64], Nev[32];
get_user_name(id, Nev, 31);
new access, callback;
menu_item_getinfo(menu, item, access, data,charsmax(data), szName,charsmax(szName), callback);
new key = str_to_num(data);

switch(key) 
	{
		case 1:
		{
		client_cmd(id, "messagemode Log_Felhasznalonev");
		}
		case 2:
		{
		client_cmd(id, "messagemode Log_Jelszo");
		}
		case 3:
        {
			if(equali(Jelszo[id], regJelszo[id]) && (equali(Felhasznalonev[id], regFh[id])))
			{
            Belepve[id] = true;
            ColorChat(id, GREEN, "%s ^1Üdv újra itt ^4%s ^1!", C_Prefix, Nev);
			}
			else
			{
			ColorChat(id, GREEN, "%s ^1Hibás Felhasználónév vagy Jelszó.", C_Prefix);
			}
        }
	}
}
public Menu_Fo(id) 
{
	new String[121];
	formatex(String, charsmax(String), "%s \r- \dRegisztrációs rendszer^n%s", Prefix, REGMENU[0][Regisztralt[id]]);
	new menu = menu_create(String, "Menu_Fo_h");
	
	formatex(String, charsmax(String), "Regisztáció^n");
	menu_additem(menu, String, "2",0);
	formatex(String, charsmax(String), "Bejelentkezés^n^n\d-------------------------------^n%s^n-------------------------------", Website);
	menu_additem(menu, String, "1",0);
	
	menu_display(id, menu, 0);
	return PLUGIN_HANDLED;
}
public Menu_Fo_h(id, menu, item)
{
if(item == MENU_EXIT)
{
menu_destroy(menu);
return;
}

new data[9], szName[64];
new access, callback;
menu_item_getinfo(menu, item, access, data,charsmax(data), szName,charsmax(szName), callback);
new key = str_to_num(data);

switch(key) 
	{
	case 1: Menu_Bejelentkezes(id);
	case 2: Menu_Regisztracio(id);
	}
}
public regisztralas_felh(id)
{
    new adat[32];
    new hosszusag = strlen(adat);
    read_args(adat, charsmax(adat));
    remove_quotes(adat);
    if(hosszusag >= 5) 
	{
        regFh[id] = adat;
        Beirtfelhasznalot[id] = true;
        Menu_Regisztracio(id);
    }
    else 
	{
        regFh[id] = adat;
        Beirtfelhasznalot[id] = true;
        Menu_Regisztracio(id);
    }
    return PLUGIN_CONTINUE;
}
public regisztralas_jelszo(id)
{
    new adat[32];
    new hosszusag = strlen(adat);
    read_args(adat, charsmax(adat));
    remove_quotes(adat);
    if(hosszusag >= 5) {
        regJelszo[id] = adat;
        Beirtjelszot[id] = true;
        Menu_Regisztracio(id);
    }
    else {
        regJelszo[id] = adat;
        Beirtjelszot[id] = true;
        Menu_Regisztracio(id);
    }
    return PLUGIN_CONTINUE;
}
public bejelentkezes_jelszo(id)
{
    new adat[32];
    new hosszusag = strlen(adat);
    read_args(adat, charsmax(adat));
    remove_quotes(adat);
    if(hosszusag >= 5) {
        Jelszo[id] = adat;
        Beirtjelszot1[id] = true;
        Menu_Bejelentkezes(id);
    }
    else {
        Jelszo[id] = adat;
        Beirtjelszot1[id] = true;
        Menu_Bejelentkezes(id);
    }
    return PLUGIN_CONTINUE;
}
public bejelentkezes_felh(id)
{
new adat[32];
new hosszusag = strlen(adat);
read_args(adat, charsmax(adat));
remove_quotes(adat);
if(hosszusag >= 5) 
{
Felhasznalonev[id] = adat;
Beirtfelhasznalot1[id] = true;
Menu_Bejelentkezes(id);
}
else 
{
Felhasznalonev[id] = adat;
Beirtfelhasznalot1[id] = true;
Menu_Bejelentkezes(id);
}
return PLUGIN_CONTINUE;
}
public Fomenu(id)
{
	new String[121];
	format(String, charsmax(String), "%s^n\dDollár: \r%.2f $ \d| SMS Pont: \r%d", Prefix, Dollar[id], SMS[id]);
	new menu = menu_create(String, "Fomenu_h");
	
	menu_additem(menu, "\y[\wRaktár\y]", "1", 0);
	menu_additem(menu, "\y[\wLádaNyitás\y]", "2", 0);
	menu_additem(menu, "\y[\wPiac/Event\y]", "3", 0);
	menu_additem(menu, "\y[\wKuka\y]", "4", 0);
	menu_additem(menu, "\y[\wSMS Pont \dLevásárlás\y]", "6", 0);
	menu_additem(menu, "\y[\rSzerver Szabályzat\y]", "7", 0);
	format(String, charsmax(String), "\y[\wBeállítások\y]", Rangok[Rang[id]][Szint], Website);
	menu_additem(menu, String, "5", 0);
	
	menu_display(id, menu, 0);
}
public Fomenu_h(id, menu, item){
	if(item == MENU_EXIT)
	{
		menu_destroy(menu);
		return;
	}
	
	new data[9], szName[64];
	new access, callback;
	menu_item_getinfo(menu, item, access, data,charsmax(data), szName,charsmax(szName), callback);
	new key = str_to_num(data);
	
	switch(key)
	{
		case 1: Item(id);
		case 2: LadaNyitas(id);
		case 3: Piac(id);
		case 4: Kuka(id);
		case 5: Beallitasok(id);
		case 6: SMS_Fomenu(id);
		case 7: Szabalyzat(id);
	}
}
public Beallitasok(id)
{
new String[121];
format(String, charsmax(String), "%s \r- \dBeállítások", Prefix);
new menu = menu_create(String, "Beallitasok_h");

menu_additem(menu, Gun[id] == true ? "Skinek: \rBekapcsolva":"Skinek: \rKikapcsolva", "1",0);
menu_additem(menu, Hud[id] == true ? "HUD: \rBekapcsolva":"HUD: \rKikapcsolva", "2",0);
menu_additem(menu, AutoB[id] == 1 ? "Automatikus Bejelentkezés: \rBekapcsolva":"Automatikus Bejelentkezés: \rKikapcsolva", "3",0);
menu_additem(menu, "Fiók Újra \rTelepités", "4", 0);

menu_display(id, menu, 0);
}
public Beallitasok_h(id, menu, item){
if(item == MENU_EXIT)
{
menu_destroy(menu);
return;
}

new data[9], szName[64];
new access, callback;
menu_item_getinfo(menu, item, access, data,charsmax(data), szName,charsmax(szName), callback);
new key = str_to_num(data);

switch(key) 
	{
		case 1: 
		{
			if(Gun[id])
			{
				Gun[id] = false;
			}
			else 
			{
				Gun[id] = true;
			}
			Beallitasok(id);
		}
		case 2: 
		{
			if(Hud[id])
			{
				Hud[id] = false;
			}
			else 
			{
				Hud[id] = true;
			}
			Beallitasok(id);
		}
		case 3: 
		{
			if(AutoB[id] == 0)
			{
				AutoB[id] = 1;
			}
			else 
			{
				AutoB[id] = 0;
			}
			Beallitasok(id);
			}
			case 4: telepiteskerdes(id);
		}
}
public telepiteskerdes(id)
	{
		new String[121];
		formatex(String, charsmax(String), "\dFiók újratelepítése");
		new menu = menu_create(String, "telepiteskerdes_h");
		
		formatex(String, charsmax(String), "\rA fiók újratelepítése, teljes törléssel jár!");
		menu_additem(menu, String, "",0);
		formatex(String, charsmax(String), "\yA törléssel mindened eltűnik!");
		menu_additem(menu, String, "",0);
		formatex(String, charsmax(String), "\rHa az igenre rámész a művelet visszafordíthatatlan!");
		menu_additem(menu, String, "",0);
		formatex(String, charsmax(String), "\yBiztosan újra szeretnéd telepíteni a fiókod?^n");
		menu_additem(menu, String, "",0);
		formatex(String, charsmax(String), "\yIgen");
		menu_additem(menu, String, "1",0);
		formatex(String, charsmax(String), "\rNem");
		menu_additem(menu, String, "2",0);
		
		menu_display(id, menu, 0);
		return PLUGIN_HANDLED;
	}
	public telepiteskerdes_h(id, menu, item){
		if(item == MENU_EXIT)
		{
			menu_destroy(menu);
			return;
		}
		
		new Nev[32]; get_user_name(id, Nev, 31);
		new data[9], szName[64];
		new access, callback;
		menu_item_getinfo(menu, item, access, data,charsmax(data), szName,charsmax(szName), callback);
		new key = str_to_num(data);
		
		switch(key) 
		{
			case 1: 
			{
			Dollar[id] = 0.0;
			SMS[id] = 0;
			Oles[id] = 0;
			
			for(new i;i < MAX; i++)
				OsszesSkin[i][id] = 0;
				
			for(new i;i < LADA; i++)
			    LadaK[i][id] = 0;
			
			for(new i;i < LADA; i++)
				Lada[i][id] = 0;
			
			for(new i;i < 5; i++)
				Skin[i][id] = 0;
			
			client_print_color(0, print_team_default, "^4%s ^1Játékos: ^4%s^1 | Újratelepítette a fiókját. Mindene törlésre került!", C_Prefix, Nev);
			}
			case 2: 
			{
				menu_destroy(menu);
		}
	}
}
public LadaNyitas(id)
{
new String[121];
formatex(String, charsmax(String), "%s \r- \dLádaNyitás", Prefix);
new menu = menu_create(String, "Lada_h");

formatex(String, charsmax(String), "\wKezdő Láda: [\r%d/DB\w] | Kezdő Kulcs: [\r%d/DB\w]", Lada[0][id], LadaK[0][id]);
menu_additem(menu, String, "1",0);
formatex(String, charsmax(String), "\wVas Láda: [\r%d/DB\w] | Vas Kulcs: [\r%d/DB\w]", Lada[1][id], LadaK[1][id]);
menu_additem(menu, String, "2",0);
formatex(String, charsmax(String), "\wGyémánt Láda: [\r%d/DB\w] | Gyémánt Kulcs: [\r%d/DB\w]", Lada[2][id], LadaK[2][id]);
menu_additem(menu, String, "3",0);
formatex(String, charsmax(String), "\wArany Láda: [\r%d/DB\w] | Arany Kulcs: [\r%d/DB\w]", Lada[3][id], LadaK[3][id]);
menu_additem(menu, String, "4",0);
formatex(String, charsmax(String), "\wGamma Láda: [\r%d/DB\w] | Gamma Kulcs: [\r%d/DB\w]^n", Lada[4][id], LadaK[4][id]);
menu_additem(menu, String, "5",0);

menu_additem(menu, "VIP \rLáda \y*Csak VIPEKNEK!", "6", ADMIN_KICK);

menu_display(id, menu, 0);
}
public Szabalyzat(id)
{
new String[121];
formatex(String, charsmax(String), "%s \r- \dSzabályzat^nUnban Kérés: \r%s", Prefix, Website);
new menu = menu_create(String, "h_szabaly");

for(new i;i < sizeof(szabaly); i++)
{
	new Sor[6]; num_to_str(i, Sor, 5);
	formatex(String, charsmax(String), "%s", szabaly[i][0]);
	menu_additem(menu, String, Sor);
}
menu_display(id, menu, 0);
}
public Lada_h(id, menu, item){
	if(item == MENU_EXIT)
	{
		menu_destroy(menu);
		return;
	}
	
	new data[9], szName[64];
	new access, callback;
	menu_item_getinfo(menu, item, access, data,charsmax(data), szName,charsmax(szName), callback);
	new key = str_to_num(data);
	
	switch(key)
	{
	    case 6: viplada(id);
        case 1:
        {
        if(Lada[0][id] >= 1 && LadaK[0][id] >= 1)
        {

        Lada[0][id]--;
        LadaK[0][id]--;
        Talal(id);
        }
	    else
        {
	    LadaNyitas(id);
	    client_print_color(id, print_team_default, "^4%s^1Nincs Ládád vagy Kulcsod.", C_Prefix);
        }
        }
        case 2:
        {
		if(Lada[1][id] >= 1 && LadaK[1][id] >= 1)
		{
		
		Lada[1][id]--;
		LadaK[1][id]--;
		Talal(id);
		}
		else
		{
		LadaNyitas(id);
		client_print_color(id, print_team_default, "^4%s^1Nincs Ládád vagy Kulcsod.", C_Prefix);
		}
		}
		case 3:
        {
		if(Lada[2][id] >= 1 && LadaK[2][id] >= 1)
		{
		
		Lada[2][id]--;
		LadaK[2][id]--;
		Talal(id);
		}
		else
		{
		LadaNyitas(id);
		client_print_color(id, print_team_default, "^4%s^1Nincs Ládád vagy Kulcsod.", C_Prefix);
		}
		}
		case 4:
        {
		if(Lada[3][id] >= 1 && LadaK[3][id] >= 1)
		{
		
		Lada[3][id]--;
		LadaK[3][id]--;
		Talal(id);
		}
		else
		{
		LadaNyitas(id);
		client_print_color(id, print_team_default, "^4%s^1Nincs Ládád vagy Kulcsod.", C_Prefix);
		}
		}
		case 5:
        {
		if(Lada[4][id] >= 1 && LadaK[4][id] >= 1)
		{
		
		Lada[4][id]--;
		LadaK[4][id]--;
		Talal(id);
		}
		else
		{
		LadaNyitas(id);
		client_print_color(id, print_team_default, "^4%s^1Nincs Ládád vagy Kulcsod.", C_Prefix);
		}
		menu_display(id, menu, 0);
	}
}	
}
public viplada(id)
{
    new String[121];
    formatex(String, charsmax(String), "VIP \rLáda", Prefix);
    new menu = menu_create(String, "viplada_h");
	
	
	formatex(String, charsmax(String), "\wVIP Láda: \r[\y%d\w Darab\r]", Lada[5][id]);
	menu_additem(menu, String, "0", 0);
	formatex(String, charsmax(String), "\wVIP Kulcs: \r[\y%d\w Darab\r]^n", LadaK[5][id]);
    menu_additem(menu, String, "0", 0);
	formatex(String, charsmax(String), "\rVIP Láda Vásárlás \r(\w-15.00\yEU\r)");
	menu_additem(menu, String, "1", 0);
	formatex(String, charsmax(String), "\rVIP Kulcs Vásárlás \r(\w-7.00\yEU\r)");
	menu_additem(menu, String, "2", 0);
	formatex(String, charsmax(String), "\rNyitás \y[\r25.00% \wKésNyitás\y]");
	menu_additem(menu, String, "3", 0);
	
	menu_display(id, menu, 0);
}
public viplada_h(id, menu, item)
{
    if(item == MENU_EXIT)
	{
		menu_destroy(menu);
		return;
	}
	
	new data[9], szName[64];
	new access, callback;
	menu_item_getinfo(menu, item, access, data,charsmax(data), szName,charsmax(szName), callback);
	new key = str_to_num(data);
	
	switch(key)
	{
	
	case 1:
	{
	if(Dollar[id] >= 15)
	{
	
	Dollar[id] -= 15;
	Lada[5][id] += 1;
	client_print_color(id, print_team_default, "^4%s ^1Vettél egy VIP Ládá-t!", C_Prefix);
	}
	else
	{
	client_print_color(id, print_team_default, "^4%s ^1Nincs elég Dollárod!", C_Prefix);
	}
	menu_display(id, menu, 0);
	}
	case 2:
	{
	if(Dollar[id] >= 7)
	{
	
	Dollar[id] -= 7;
	LadaK[5][id] += 1;
	client_print_color(id, print_team_default, "^4%s ^1Vettél egy VIP Kulcsot!", C_Prefix);
	}
	else
	{
	client_print_color(id, print_team_default, "^4%s ^1Nincs elég Dollárod!", C_Prefix);
	}
	menu_display(id, menu, 0);
	}
	case 3:
	{
	if(Lada[5][id] >= 1 && LadaK[5][id] >= 1)
    {
	Lada[5][id]--;
	LadaK[5][id]--;
	Talal(id);
	viplada(id);
	}
	else
    {
	viplada(id);
	client_print_color(id, print_team_default, "^4%s^1Nincs Ládád vagy Kulcsod.", C_Prefix);
}
menu_display(id, menu, 0);
}
}
}
public h_szabaly(id, menu, item){
	if(item == MENU_EXIT)
	{
		menu_destroy(menu);
		return;
	}
	
	new data[9], szName[64];
	new access, callback;
	menu_item_getinfo(menu, item, access, data,charsmax(data), szName,charsmax(szName), callback);
	
	Szabalyzat(id);
	
}
public Talal(id)
{
new Nev[32]; get_user_name(id, Nev, 31);
new Float:Szam = random_float(0.01,100.0);
new FegyverID = random_num(0, 82);
new KesID = random_num(83, MAX);

if(Szam <= KESDROP)
{
	OsszesSkin[KesID][id]++;
	ColorChat(id, GREEN, "%s ^1Nyitottál egy ^4%s ^1skint", C_Prefix, Fegyverek[KesID]);
	ColorChat(0, GREEN, "%s ^3%s ^1Nyitott egy kést", C_Prefix, Nev);
}
else
{
	OsszesSkin[FegyverID][id]++;
	ColorChat(id, GREEN, "%s ^1Nyitottál egy ^4%s ^1skint", C_Prefix, Fegyverek[FegyverID]);
}
}
public Item(id)
{
    new String[121];
	format(String, charsmax(String), "Raktár Menü", Prefix, SMS[id]);
	new menu = menu_create(String, "Item_h");
	
	formatex(String, charsmax(String), "Szerencse \rJátékok");
	menu_additem(menu, String, "2", 0);
	formatex(String, charsmax(String), "Skin \yRaktár");
	menu_additem(menu, String, "1", 0);
	formatex(String, charsmax(String), "Szerencseérmék");
	menu_additem(menu, String, "0", 0);
	formatex(String, charsmax(String), "Játékos \rModellek");
	menu_additem(menu, String, "0", 0);
	formatex(String, charsmax(String), "Dollár \rVáltó");
	menu_additem(menu, String, "3", 0);
	
	menu_display(id, menu, 0);
}
public Item_h(id, menu, item)
{
if(item == MENU_EXIT)
	{
		menu_destroy(menu);
		return;
	}
	
	new data[9], szName[64];
	new access, callback;
	menu_item_getinfo(menu, item, access, data,charsmax(data), szName,charsmax(szName), callback);
	new key = str_to_num(data);
	
	switch(key)
	{
	case 1: Raktar(id);
	case 2: Szerencsejatekok(id);
	case 3: Dollarvalto(id);
}	
}
public Dollarvalto(id)
{
    new String[121];
	format(String, charsmax(String), "Dollár \rVáltó");
	new menu = menu_create(String, "Dollarvalto_h");
	
	menu_additem(menu, "1 SMS Pont \y[\r10.00USD\y]", "1", 0);
	menu_additem(menu, "5 SMS Pont \y[\r35.00USD\y]", "2", 0);
	menu_additem(menu, "10 SMS Pont \y[\r60.00USD\y]", "3", 0);
	menu_additem(menu, "15 SMS Pont \y[\r85.00USD\y]", "4", 0);
	menu_additem(menu, "20 SMS Pont \y[\r110.00USD\y]", "5", 0);
	menu_additem(menu, "25 SMS Pont \y[\r150.00USD\y]", "6", 0);
	menu_additem(menu, "30 SMS Pont \y[\r185.00USD\y]", "7", 0);

       menu_display(id, menu, 0);
}
public Dollarvalto_h(id, menu, item)
{
   	if(item == MENU_EXIT)
	{
		menu_destroy(menu);
		return;
	}
	
	new data[9], szName[64];
	new access, callback;
	menu_item_getinfo(menu, item, access, data,charsmax(data), szName,charsmax(szName), callback);
	new key = str_to_num(data);
	
	switch(key)
	{
	       case 1: 
	       {
	       if(SMS[id] >= 1)
	       {
	       
	       SMS[id] -= 1;
	       Dollar[id] += 10;
	       client_print_color(id, print_team_default, "^4%s ^1Sikeresen váltotál 10.00USD-t!", C_Prefix);
	       }
	       else
	       {
	       client_print_color(id, print_team_default, "^4%s ^1Nincs elég SMS Pontod!", C_Prefix);
	       }
	       }
	       case 2: 
	       {
	       if(SMS[id] >= 5)
	       {
	       
	       SMS[id] -= 5;
	       Dollar[id] += 35;
	       client_print_color(id, print_team_default, "^4%s ^1Sikeresen váltotál 35.00USD-t!", C_Prefix);
	       }
	       else
	       {
	       client_print_color(id, print_team_default, "^4%s ^1Nincs elég SMS Pontod!", C_Prefix);
	       }
	       }
	       case 3: 
	       {
	       if(SMS[id] >= 10)
	       {
	       
	       SMS[id] -= 10;
	       Dollar[id] += 60;
	       client_print_color(id, print_team_default, "^4%s ^1Sikeresen váltotál 60.00USD-t!", C_Prefix);
	       }
	       else
	       {
	       client_print_color(id, print_team_default, "^4%s ^1Nincs elég SMS Pontod!", C_Prefix);
	       }
	       }
	       case 4: 
	       {
	       if(SMS[id] >= 15)
	       {
	       
	       SMS[id] -= 15;
	       Dollar[id] += 85;
	       client_print_color(id, print_team_default, "^4%s ^1Sikeresen váltotál 85.00USD-t!", C_Prefix);
	       }
	       else
	       {
	       client_print_color(id, print_team_default, "^4%s ^1Nincs elég SMS Pontod!", C_Prefix);
	       }
	       }
	       case 5: 
	       {
	       if(SMS[id] >= 20)
	       {
	       
	       SMS[id] -= 20;
	       Dollar[id] += 110;
	       client_print_color(id, print_team_default, "^4%s ^1Sikeresen váltotál 110.00USD-t!", C_Prefix);
	       }
	       else
	       {
	       client_print_color(id, print_team_default, "^4%s ^1Nincs elég SMS Pontod!", C_Prefix);
	       }
	       }
	       case 6: 
	       {
	       if(SMS[id] >= 25)
	       {
	       
	       SMS[id] -= 25;
	       Dollar[id] += 150;
	       client_print_color(id, print_team_default, "^4%s ^1Sikeresen váltotál 150.00USD-t!", C_Prefix);
	       }
	       else
	       {
	       client_print_color(id, print_team_default, "^4%s ^1Nincs elég SMS Pontod!", C_Prefix);
	       }
	       }
	       case 7: 
	       {
	       if(SMS[id] >= 30)
	       {
	       
	       SMS[id] -= 30;
	       Dollar[id] += 185;
	       client_print_color(id, print_team_default, "^4%s ^1Sikeresen váltotál 185.00USD-t!", C_Prefix);
	       }
	       else
	       {
	       client_print_color(id, print_team_default, "^4%s ^1Nincs elég SMS Pontod!", C_Prefix);
	       }
	       }
}
}
public Szerencsejatekok(id)
{
    new String[121];
	format(String, charsmax(String), "Szerencse \rJátékok");
	new menu = menu_create(String, "Szerencsejatekok_h");
	
	menu_additem(menu, "\rRulette", "1", 0);
    menu_additem(menu, "\rGambling", "2", 0);
	menu_display(id, menu, 0);
}
public Szerencsejatekok_h(id, menu, item){
	if(item == MENU_EXIT)
	{
		menu_destroy(menu);
		return;
	}
	
	new data[9], szName[64];
	new access, callback;
	menu_item_getinfo(menu, item, access, data,charsmax(data), szName,charsmax(szName), callback);
	new key = str_to_num(data);

	
	
	
	switch(key)
    {
        case 1: rulimenu(id);
		case 2: rulette(id);
	}
}
public rulette(id)
{
new String[121];
format(String, charsmax(String), "\w[\r%s]-\w Gambling^n\rDollár: \y%.2f", C_Prefix, Dollar[id]);
new menu = menu_create(String, "rulette_h");

menu_additem(menu, "\d \r| \yGambling\r |\w5.00\d Dollár \r| \d^n", "1", 0);
menu_additem(menu, "\d \r| \yVigyáz mert \r| \d", "2", 0);
menu_additem(menu, "\d \r| \yNyerhetsz\y/\wde-Veszíthetsz\r Dollárt!! \r| \d", "2", 0);
menu_additem(menu, "\d \r| \yMin\r 0.00\w Dollár\y |\w Max\d 30.00\w Dollár \r| \d", "2", 0);

menu_display(id, menu, 0);
return PLUGIN_HANDLED;
}
public rulette_h(id, menu, item){
if(item == MENU_EXIT)
{
menu_destroy(menu);
}

new data[9], szName[64];
new access, callback;
menu_item_getinfo(menu, item, access, data,charsmax(data), szName,charsmax(szName), callback);
new key = str_to_num(data);

switch(key)
{
case 2: rulette(id);
case 1:
{
switch(random_num(1,10))
{
case 1:
{
if(Dollar[id] >= 5)
{
Dollar[id] -= 5;
Dollar[id] += 20;
ColorChat(id, GREEN, "%s^1 Gratulálok nyertél^3 +20.00^1 Dollárt", C_Prefix);
}
else
{
ColorChat(id, GREEN, "%s ^1Nincs elég Dollárod", C_Prefix);
}
}
case 2:
{
if(Dollar[id] >= 5)
{
Dollar[id] -= 5;
Dollar[id] += 7;
ColorChat(id, GREEN, "%s^1 Gratulálok nyertél^3 +7.00^1 Dollárt", C_Prefix);
}
else
{
ColorChat(id, GREEN, "%s ^1Nincs elég Dollárod", C_Prefix);
}
}
case 3:
{
if(Dollar[id] >= 5)
{
Dollar[id] -= 5;
Dollar[id] += 0;
ColorChat(id, GREEN, "%s^1Sajnos te^3 VESZTETTÉL", C_Prefix);
}
else
{
ColorChat(id, GREEN, "%s ^1Nincs elég Dollárod", C_Prefix);
}
}
case 4:
{
if(Dollar[id] >= 5)
{
Dollar[id] -= 5;
Dollar[id] += 0;
ColorChat(id, GREEN, "%s^1 Sajnos te^3 VESZTETTÉL", C_Prefix);
}
else
{
ColorChat(id, GREEN, "%s ^1Nincs elég Dollárod", C_Prefix); 
}
}
case 5:
{
if(Dollar[id] >= 5)
{
Dollar[id] -= 5;
Dollar[id] += 3;
ColorChat(id, GREEN, "%s^1 Gratulálok nyertél^3 +3.00^1 Dollárt", C_Prefix);
}
else
{
ColorChat(id, GREEN, "%s ^1Nincs elég Dollárod", C_Prefix);
}
}
case 6:
{
if(Dollar[id] >= 5)
{
Dollar[id] -= 5;
Dollar[id] += 30;
ColorChat(id, GREEN, "%s^1 Gratulálok nyertél^3 +30.00^1 Dollárt", C_Prefix);
}
else
{
ColorChat(id, GREEN, "%s ^1Nincs elég Dollárod", C_Prefix);
}
}
case 7:
{
if(Dollar[id] >= 5)
{
Dollar[id] -= 5;
Dollar[id] += 0;
ColorChat(id, GREEN, "%s^1 Sajnos te^3 VESZTETTÉL", C_Prefix);
}
else
{
ColorChat(id, GREEN, "%s ^1Nincs elég Dollárod", C_Prefix);
}
}
case 8:
{
if(Dollar[id] >= 5)
{
Dollar[id] -= 5;
Dollar[id] += 0;
ColorChat(id, GREEN, "%s^1 Sajnos te^3 VESZTETTÉL", C_Prefix);
}
else
{
ColorChat(id, GREEN, "%s ^1Nincs elég Dollárod", C_Prefix);
}
}
case 9:
{
if(Dollar[id] >= 5)
{
Dollar[id] -= 5;
Dollar[id] += 10;
ColorChat(id, GREEN, "%s^1 Gratulálok nyertél^3 +10.00^1 Dollárt", C_Prefix);
}
else
{
ColorChat(id, GREEN, "%s ^1Nincs elég Dollárod", C_Prefix);
}
}
case 10:
{
if(Dollar[id] >= 5)
{
Dollar[id] -= 5;
Dollar[id] += 15;
ColorChat(id, GREEN, "%s^1 Gratulálok nyertél^3 +15.00^1 Dollárt", C_Prefix);
}
else
{
ColorChat(id, GREEN, "%s ^1Nincs elég Dollárod", C_Prefix);
}}}}}}
new berakott[33];
public rulimenu(id)
{
new focim[121];
formatex(focim, charsmax(focim), "\d[\y\d] \wRoulette^n\wDollár: \r%.2f", Dollar[id]);
new menu = menu_create(focim, "roul_menu");
 
 
format(focim,charsmax(focim),"\wPiros 2x^n\r1-7-ig | \wTéted: \r%d^n", Berakepiros[id]);
menu_additem(menu,focim,"0");
format(focim,charsmax(focim),"\wSzürke 2x^n\d8-14-ig | \wTéted: \r%d^n", Berakeszurke[id]);
menu_additem(menu,focim,"1");
format(focim,charsmax(focim),"\wZöld 14x^nCsak \y0 | \wTéted: \r%d^n^n", Berakezold[id]);
menu_additem(menu,focim,"2");
if(berakott[id] > 0)
{
format(focim,charsmax(focim),"\ySPIN");
menu_additem(menu,focim,"3");
}
else
{
}
 
menu_setprop(menu, MPROP_EXIT, MEXIT_ALL);
menu_setprop(menu, MPROP_EXITNAME, "Kilépés");
 
menu_display(id, menu, 0);
return PLUGIN_HANDLED;
}
public rulilekeres(id) {
    new Berakertekpiros, adatok[32];
    read_args(adatok, charsmax(adatok));
    remove_quotes(adatok);
   
    Berakertekpiros = str_to_num(adatok);
   
    if(str_to_num(adatok) < 1)
    return PLUGIN_HANDLED;
   
    if(Dollar[id] >= str_to_num(adatok) && berakott[id] <= 2)
    {
        Berakepiros[id] = Berakertekpiros;
        Dollar[id] -= Berakertekpiros;
        berakott[id]++;
        rulimenu(id);
    }
    else if(Berakepiros[id] < 6)
    {
       ColorChat(id, GREEN, "[INFO] ^1Minimum bet: 1EU");
        rulimenu(id);
    }
    return PLUGIN_HANDLED;
}
public rulilekeres1(id) {
    new Berakertekszurke, adatok[32];
    read_args(adatok, charsmax(adatok));
    remove_quotes(adatok);
   
    Berakertekszurke = str_to_num(adatok);
    if(str_to_num(adatok) < 1)
    return PLUGIN_HANDLED;
   
   
    if(Dollar[id] >= str_to_num(adatok) && berakott[id] <= 2)
    {
        Berakeszurke[id] = Berakertekszurke;
        Dollar[id] -= Berakertekszurke;
        berakott[id]++;
        rulimenu(id);
    }
    else if(Berakeszurke[id] < 6)
    {
        ColorChat(id, GREEN, "[Info] ^1Minimum bet: 1EU");
        rulimenu(id);
    }
    return PLUGIN_HANDLED;
}
public rulilekeres2(id) {
    new Berakertekzold, adatok[32];
    read_args(adatok, charsmax(adatok));
    remove_quotes(adatok);
   
    Berakertekzold = str_to_num(adatok);
    if(str_to_num(adatok) < 1)
    return PLUGIN_HANDLED;
   
   
    if(Dollar[id] >= str_to_num(adatok) && berakott[id] <= 2)
    {
        Berakezold[id] = Berakertekzold;
        Dollar[id] -= Berakertekzold;
        berakott[id]++;
        rulimenu(id);
    }
    else if(Berakezold[id] < 6)
    {
       ColorChat(id, GREEN, "[Info] ^1Minimum bet: 5");
        rulimenu(id);
    }
    return PLUGIN_HANDLED;
}
public roul_menu(id, menu, item){
if(item == MENU_EXIT)
{
    menu_destroy(menu);
    return;
}
   
new data[9], szName[64];
new access, callback;
menu_item_getinfo(menu, item, access, data,charsmax(data), szName,charsmax(szName), callback);
new key = str_to_num(data);
   
switch(key)
{
case 0:
{
if(berakott[id] <= 2)
{
client_cmd(id, "messagemode BET");
Dollar[id] -= Berakepiros[id];
rulimenu(id);
}
else
{
ColorChat(id, GREEN, "^4[.::*[Dragon*.*KinG]*::.]^3PontFLIP: ^1Csak egy valasztási lehetőséged van!");
}
}
case 1:
{
if(berakott[id] <= 2)
{
client_cmd(id, "messagemode BET1");
Dollar[id] -= Berakeszurke[id];
rulimenu(id);
}
else
{
ColorChat(id, GREEN, "^4[.::*[Dragon*.*KinG]*::.]^3PontFLIP: ^1Csak egy valasztási lehetőséged van!");
}
}
case 2:
{
if(berakott[id] <= 2)
{
client_cmd(id, "messagemode BET2");
Dollar[id] -= Berakezold[id];
rulimenu(id);
}
else
{
ColorChat(id, GREEN, "^4[.::*[Dragon*.*KinG]*::.]^3PontFLIP: ^1Csak egy valasztási lehetőséged van!");
}
}
case 3:
{
new Pontsorsolas = random_num(0,14);
if(Pontsorsolas >= 1 && Pontsorsolas <= 7 && berakott[id] <= 2)
{
Dollar[id] += Berakepiros[id]*2;
}
else if(Pontsorsolas == 0 && berakott[id] <= 2)
{
Dollar[id] += Berakezold[id]*14;
}
else if(Pontsorsolas >= 8 && Pontsorsolas <= 14 && berakott[id] <= 2)
{
Dollar[id] += Berakeszurke[id]*2;
}
ColorChat(id, GREEN, "^4[.::*[Dragon*.*KinG]*::.]^3ROULETTE: ^1A Nyerőszám: ^3%d", Pontsorsolas);
Berakeszurke[id] = 0;
Berakepiros[id] = 0;
Berakezold[id] = 0;
berakott[id] = 0;
}
}

}	
public SMS_Fomenu(id)
{
	new String[121];
	format(String, charsmax(String), "%s^n\dSMS Pont: \r%d", Prefix, SMS[id]);
	new menu = menu_create(String, "SMS_Fomenu_h");
	
	menu_additem(menu, "\rSMS Pont Vásárlás", "1", 0);
	menu_additem(menu, "Admin Jog Vásárlás", "5", 0);
	menu_additem(menu, "Random \dKés Drop \d[\r254 SMS Pont\d]", "2", 0);
	if(Vip[id] >= 1)
	{
	menu_additem(menu, "VIP Jog \d[\rMEGVÁSÁROLVA\d] - Csak akkor tellik ha fenntvagy!^nNapi 2 óra játékkal 84 napig tart!", "", 0);
	}
	else
	{
	menu_additem(menu, "VIP Jog \d[\r1 Hét | 508 SMS Pont\d] - Csak akkor tellik ha fenntvagy!^nNapi 2 óra játékkal 84 napig tart!", "3", 0);
	}
	menu_additem(menu, "+50 Kulcs \d[\r254 SMS Pont\d]", "4", 0);
	
	menu_display(id, menu, 0);
}
public SMS_Fomenu_h(id, menu, item){
	if(item == MENU_EXIT)
	{
		menu_destroy(menu);
		return;
	}
	
	new data[9], szName[64];
	new access, callback;
	menu_item_getinfo(menu, item, access, data,charsmax(data), szName,charsmax(szName), callback);
	new key = str_to_num(data);
	new KesID = random_num(83,MAX);
	
	switch(key)
	{
		case 1:
		{
			SMS_Vasarlas(id);
		}
		case 2:
		{
			if(SMS[id] >= 254)
			{
				OsszesSkin[KesID][id]++;
				SMS[id] -= 254;
				ColorChat(id, GREEN, "%s ^1Random droppod egy ^4%s ^1skin", C_Prefix, Fegyverek[KesID]);
			}
			else
			{
				ColorChat(id, GREEN, "%s ^1Nincs elég SMS Pontod", C_Prefix);
			}
		}
		case 3:
		{
			if(SMS[id] >= 508)
			{
				Vip[id] = 0;
				Vip[id] += 7*86400;
				SMS[id] -= 508;
				ColorChat(id, GREEN, "%s ^1Megvásároltad a ^4VIP JOG^1-t a szerverre 1 hétre ^3(Csak akkor fog telleni ha fenntvagy a szerveren)", C_Prefix);
			}
			else
			{
				ColorChat(id, GREEN, "%s ^1Nincs elég SMS Pontod", C_Prefix);
			}
		}
		case 4:
		{
			if(SMS[id] >= 254)
			{
				SMS[id] -= 254;
				Kulcs[id] += 50;
				ColorChat(id, GREEN, "%s ^1Kaptál +50 ^4Kulcs^1-t", C_Prefix);
			}
			else
			{
				ColorChat(id, GREEN, "%s ^1Nincs elég SMS Pontod", C_Prefix);
			}
		}
		case 5:
		{
			Admin_Vasarlas(id);
		}
	}
}

public SMS_Vasarlas(id)
{
	new String[121];
	format(String, charsmax(String), "%s \r- \dSMS Pont Vásárlás", Prefix, SMS[id]);
	new menu = menu_create(String, "SMS_Fomenu_h");
	
	menu_additem(menu, "\r508 SMS Pont \dFeltöltés \r(480 Ft)", "1", 0);
	menu_additem(menu, "\ySzöveg: \dSYN droplexx \yTel.Szám: \d06-90/888-355", "1", 0);
	menu_additem(menu, "\r1016 SMS Pont \dFeltöltés \r(960 Ft)", "1", 0);
	menu_additem(menu, "\ySzöveg: \dSYN droplexx \yTel.Szám: \d06-90/888-466", "1", 0);
	menu_additem(menu, "--------------------------------------------------", "1", 0);
	menu_additem(menu, "Az \ySMS\w-ről \ykészíts egy képet\w majd \yküld ide\w:", "1", 0);
	menu_additem(menu, "\dfacebook.com/farkas.david.5494?ref=bookmarks \r(Morison_:D)", "1", 0);
	
	menu_display(id, menu, 0);
}
public Admin_Vasarlas(id)
{
	new String[121];
	format(String, charsmax(String), "%s \r- \dAdmin Jog Vásárlás", Prefix, SMS[id]);
	new menu = menu_create(String, "SMS_Fomenu_h");
	
	menu_additem(menu, "Admin Jog \d[\r1 Hónap | 960Ft\d]", "5", 0);
	menu_additem(menu, "\ySzöveg: \dSYN droplexx \yTel.Szám: \d06-90/888-466", "5", 0);
	menu_additem(menu, "--------------------------------------------------", "5", 0);
	menu_additem(menu, "Az \ySMS\w-ről \ykészíts egy képet\w majd \yküld ide\w:", "5", 0);
	menu_additem(menu, "\dfacebook.com/farkas.david.5494?ref=bookmarks \r(Morison_:D)", "5", 0);
	
	menu_display(id, menu, 0);
}
public Raktar(id)
{
	new String[121];
	formatex(String, charsmax(String), "%s \r- \dRaktár", Prefix);
	new menu = menu_create(String, "Raktar_h");
	
	for(new i;i < sizeof(Fegyverek); i++)
	{
		if(OsszesSkin[i][id] > 0)
		{
			new Sor[6]; num_to_str(i, Sor, 5);
			formatex(String, charsmax(String), "%s \d[\r%d DB \d]", Fegyverek[i][0], OsszesSkin[i][id]);
			menu_additem(menu, String, Sor);
		}
	}
	menu_display(id, menu, 0);
}
public Raktar_h(id, menu, item){
	if(item == MENU_EXIT)
	{
		menu_destroy(menu);
		return;
	}
	
	new data[9], szName[64];
	new access, callback;
	menu_item_getinfo(menu, item, access, data,charsmax(data), szName,charsmax(szName), callback);
	new key = str_to_num(data);
	
	switch(key) {
		case 0: Skin[0][id] = 1;
		case 1: Skin[0][id] = 2;
		case 2: Skin[0][id] = 3;
		case 3: Skin[0][id] = 4;
		case 4: Skin[0][id] = 5;
		case 5: Skin[0][id] = 6;
		case 6: Skin[0][id] = 7;
		case 7: Skin[0][id] = 8;
		case 8: Skin[0][id] = 9;
		case 9: Skin[0][id] = 10;
		case 10: Skin[0][id] = 11;
		case 11: Skin[0][id] = 12;
		case 12: Skin[0][id] = 13;
		case 13: Skin[0][id] = 14;
		case 14: Skin[0][id] = 15;
		case 15: Skin[0][id] = 16;
		case 16: Skin[0][id] = 17;
		case 17: Skin[0][id] = 18;
		case 18: Skin[0][id] = 19;
	    case 19: Skin[0][id] = 20;
		case 20: Skin[0][id] = 21;
		case 21: Skin[0][id] = 22;
		case 22: Skin[0][id] = 23;
		case 23: Skin[1][id] = 1;
		case 24: Skin[1][id] = 2;
		case 25: Skin[1][id] = 3;
		case 26: Skin[1][id] = 4;
		case 27: Skin[1][id] = 5;
		case 28: Skin[1][id] = 6;
		case 29: Skin[1][id] = 7;
		case 30: Skin[1][id] = 8;
		case 31: Skin[1][id] = 9;
		case 32: Skin[1][id] = 10;
		case 33: Skin[1][id] = 11;
		case 34: Skin[1][id] = 12;
		case 35: Skin[1][id] = 13;
		case 36: Skin[1][id] = 14;
		case 37: Skin[1][id] = 15;
		case 38: Skin[1][id] = 16;
		case 39: Skin[1][id] = 17;
		case 40: Skin[1][id] = 18;
        case 41: Skin[1][id] = 19;
	    case 42: Skin[1][id] = 20;
		case 43: Skin[1][id] = 21;
		case 44: Skin[1][id] = 22;
		case 45: Skin[1][id] = 23;
		case 46: Skin[2][id] = 1;
		case 47: Skin[2][id] = 2;
		case 48: Skin[2][id] = 3;
		case 49: Skin[2][id] = 4;
		case 50: Skin[2][id] = 5;
		case 51: Skin[2][id] = 6;
		case 52: Skin[2][id] = 7;
		case 53: Skin[2][id] = 8;
		case 54: Skin[2][id] = 9;
		case 55: Skin[2][id] = 10;
		case 56: Skin[2][id] = 11;
		case 57: Skin[2][id] = 12;
		case 58: Skin[2][id] = 13;
		case 59: Skin[2][id] = 14;
		case 60: Skin[2][id] = 15;
		case 61: Skin[2][id] = 16;
		case 62: Skin[2][id] = 17;
		case 63: Skin[2][id] = 18;
		case 64: Skin[2][id] = 19;
		case 65: Skin[2][id] = 20;
		case 66: Skin[2][id] = 21;
		case 67: Skin[2][id] = 22;
		case 68: Skin[2][id] = 23;
		case 69: Skin[2][id] = 24;
		case 70: Skin[3][id] = 1;
		case 71: Skin[3][id] = 2;
		case 72: Skin[3][id] = 3;
		case 73: Skin[3][id] = 4;
		case 74: Skin[3][id] = 5;
		case 75: Skin[3][id] = 6;
		case 76: Skin[3][id] = 7;
		case 77: Skin[3][id] = 8;
		case 78: Skin[3][id] = 9;
		case 79: Skin[3][id] = 10;
		case 80: Skin[3][id] = 11;
		case 81: Skin[3][id] = 12;
		case 82: Skin[3][id] = 13;
		case 83: Skin[3][id] = 14;
		case 84: Skin[3][id] = 15;
		case 85: Skin[3][id] = 16;
		case 86: Skin[3][id] = 17;
		case 87: Skin[3][id] = 18;
		case 88: Skin[3][id] = 19;
		case 89: Skin[3][id] = 20;
		case 90: Skin[3][id] = 21;
		case 91: Skin[3][id] = 22;
		case 92: Skin[3][id] = 23;
		case 93: Skin[3][id] = 24;
		case 94: Skin[4][id] = 1;
		case 95: Skin[4][id] = 2;
		case 96: Skin[4][id] = 3;
		case 97: Skin[4][id] = 4;
		case 98: Skin[4][id] = 5;
		case 99: Skin[4][id] = 6;
		case 100: Skin[4][id] = 7;
		case 101: Skin[4][id] = 8;
		case 102: Skin[4][id] = 9;
		case 103: Skin[4][id] = 10;
		case 104: Skin[4][id] = 11;
		case 105: Skin[4][id] = 12;
		case 106: Skin[4][id] = 13;
		case 107: Skin[4][id] = 14;
		case 108: Skin[4][id] = 15;
		case 109: Skin[4][id] = 16;
		case 110: Skin[4][id] = 17;
		case 111: Skin[4][id] = 18;
		case 112: Skin[4][id] = 19;
		case 113: Skin[4][id] = 20;
	}
}
public Kuka(id)
{
	new String[121];
	formatex(String, charsmax(String), "%s \r- \dKuka", Prefix);
	new menu = menu_create(String, "Kuka_h");
	
	for(new i;i < sizeof(Fegyverek); i++)
	{
		if(OsszesSkin[i][id] > 0)
		{
			new Sor[6]; num_to_str(i, Sor, 5);
			formatex(String, charsmax(String), "%s \d[\r%d DB\d]", Fegyverek[i][0], OsszesSkin[i][id]);
			menu_additem(menu, String, Sor);
		}
	}
	menu_display(id, menu, 0);
}
public Kuka_h(id, menu, item){
	if(item == MENU_EXIT)
	{
		menu_destroy(menu);
		return;
	}
	
	new data[9], szName[64];
	new access, callback;
	menu_item_getinfo(menu, item, access, data,charsmax(data), szName,charsmax(szName), callback);
	new key = str_to_num(data);
	
	OsszesSkin[key][id] --;
	ColorChat(id, GREEN, "%s ^1Törölted a ^4%s ^1skined", C_Prefix, Fegyverek[key][0]);
	Kuka(id);
}
public Piac(id)
{
	new String[121];
	format(String, charsmax(String), "%s \r- \dPiac^n\dDollár: \r%.2f $", Prefix, Dollar[id]);
	new menu = menu_create(String, "Piac_h");
	
	menu_additem(menu, "Eladás", "1", 0);
	menu_additem(menu, "Vásárlás", "2", 0);
	menu_additem(menu, "Tárgyak Küldése", "3", 0);
	
	menu_display(id, menu, 0);
}
public Piac_h(id, menu, item){
	if(item == MENU_EXIT)
	{
		menu_destroy(menu);
		return;
	}
	
	new data[9], szName[64];
	new access, callback;
	menu_item_getinfo(menu, item, access, data,charsmax(data), szName,charsmax(szName), callback);
	new key = str_to_num(data);
	
	switch(key)
	{
		case 1: Eladas(id);
		case 2: Vasarlas(id);
		case 3: SendMenu(id);
	}
}
public SendMenu(id) 
{
	new String[121];
	format(String, charsmax(String), "%s \r- \dTárgyak Küldése", Prefix);
	new menu = menu_create(String, "SendHandler");
	
	format(String, charsmax(String), "Dollár \d[\r%d $\d]", Dollar[id]);
	menu_additem(menu, String, "0", 0);
	format(String, charsmax(String), "Kulcs \d[\r%d DB\d]", Kulcs[id]);
	menu_additem(menu, String, "1", 0);
	format(String, charsmax(String), "SMS Pont \d[\r%d DB\d]", SMS[id]);
	menu_additem(menu, String, "2", 0);
	format(String, charsmax(String), "%s \d[\r%d DB\d]", l_Nevek[0], Lada[0][id]);
	menu_additem(menu, String, "3", 0);
	format(String, charsmax(String), "%s \d[\r%d DB\d]", l_Nevek[1], Lada[1][id]);
	menu_additem(menu, String, "4", 0);
	format(String, charsmax(String), "%s \d[\r%d DB\d]", l_Nevek[2], Lada[2][id]);
	menu_additem(menu, String, "5", 0);
	format(String, charsmax(String), "%s \d[\r%d DB\d]", l_Nevek[3], Lada[3][id]);
	menu_additem(menu, String, "6", 0);
	format(String, charsmax(String), "%s \d[\r%d DB\d]", l_Nevek[4], Lada[4][id]);
	menu_additem(menu, String, "7", 0);
	format(String, charsmax(String), "%s \d[\r%d DB\d]", l_Nevek[5], Lada[5][id]);
	menu_additem(menu, String, "8", 0);
	format(String, charsmax(String), "%s \d[\r%d DB\d]", KulcsNevek[0], LadaK[0][id]);
	menu_additem(menu, String, "9", 0);
	format(String, charsmax(String), "%s \d[\r%d DB\d]", KulcsNevek[1], LadaK[1][id]);
	menu_additem(menu, String, "10", 0);
	format(String, charsmax(String), "%s \d[\r%d DB\d]", KulcsNevek[2], LadaK[2][id]);
	menu_additem(menu, String, "11", 0);
	format(String, charsmax(String), "%s \d[\r%d DB\d]", KulcsNevek[3], LadaK[3][id]);
	menu_additem(menu, String, "12", 0);
	format(String, charsmax(String), "%s \d[\r%d DB\d]", KulcsNevek[4], LadaK[4][id]);
	menu_additem(menu, String, "13", 0);
	
	menu_display(id, menu, 0);
	return PLUGIN_HANDLED;
}
public SendHandler(id, Menu, item) {
	if(item == MENU_EXIT)
	{
		menu_destroy(Menu);
		return PLUGIN_HANDLED;
	}
	
	new Data[9], szName[64];
	new access, callback;
	menu_item_getinfo(Menu, item, access, Data,charsmax(Data), szName,charsmax(szName), callback);
	new Key = str_to_num(Data);
	
	Send[id] = Key+1;
	
	PlayerChoose(id);
	return PLUGIN_HANDLED;
}
public PlayerChoose(id)
{
	new String[121];
	format(String, charsmax(String), "%s \r- \dVálassz Játékost", Prefix);
	new Menu = menu_create(String, "PlayerHandler");
	
	new players[32], pnum, tempid;
	new szName[32], szTempid[10];
	get_players(players, pnum);
	
	for( new i; i<pnum; i++ )
	{
		tempid = players[i];
		{
			get_user_name(tempid, szName, charsmax(szName));
			num_to_str(tempid, szTempid, charsmax(szTempid));
			menu_additem(Menu, szName, szTempid, 0);
		}
	}
	
	menu_display(id, Menu, 0);
	return PLUGIN_HANDLED;
}
public PlayerHandler(id, Menu, item)
{
	if( item == MENU_EXIT )
	{
		menu_destroy(Menu);
		return PLUGIN_HANDLED;
	}
	new Data[6], szName[64];
	new access, callback;
	menu_item_getinfo(Menu, item, access, Data,charsmax(Data), szName,charsmax(szName), callback);
	TempID = str_to_num(Data);
	
	client_cmd(id, "messagemode KMENNYISEG");
	
	menu_destroy(Menu);
	return PLUGIN_HANDLED;
}
public Eladas(id) {
	new cim[121], ks1[121], ks2[121];
	format(cim, charsmax(cim), "%s \r- \dEladás", Prefix);
	new menu = menu_create(cim, "eladas_h" );
	
	if(kirakva[id] == 0){
		for(new i=0; i < MAX; i++) {
			if(kicucc[id] == 0) format(ks1, charsmax(ks1), "Válaszd ki a Tárgyat!");
			else if(kicucc[id] == i) format(ks1, charsmax(ks1), "Tárgy: \r%s", Fegyverek[i-1][0]);
		}
		menu_additem(menu, ks1 ,"0",0);
	}
	if(kirakva[id] == 0){
		format(ks2, charsmax(ks2), "\dÁra: \r%.2f.00 \yDOLLÁR", Erteke[id]);
		menu_additem(menu,ks2,"1",0);
	}
	if(Erteke[id] != 0 && kirakva[id] == 0)
	{
		menu_additem(menu,"Mehet a piacra!","2",0);
	}
	if(Erteke[id] != 0 && kirakva[id] == 1)
		menu_additem(menu,"\wVisszavonás","-2",0);
	
	menu_setprop(menu, MPROP_EXITNAME, "Kilépés");
	menu_setprop(menu, MPROP_EXIT, MEXIT_ALL);
	menu_display(id, menu, 0);
}
public eladas_h(id, menu, item){
	if( item == MENU_EXIT )
	{
		menu_destroy(menu);
		return PLUGIN_HANDLED;
	}
	new data[9], szName[64], name[32];
	get_user_name(id, name, charsmax(name));
	new access, callback;
	menu_item_getinfo(menu, item, access, data,charsmax(data), szName,charsmax(szName), callback);
	new key = str_to_num(data);
	
	switch(key)
	{
		case -2:{
			kirakva[id] = 0;
			kicucc[id] = 0;
			Erteke[id] = 0;
		}
		case 0:{
			fvalaszt(id);
		}
		case 1:{
			client_cmd(id, "messagemode DOLLAR");
		}
		case 2:{
			for(new i=0; i < MAX; i++)
			{
				if(kicucc[id] == i && OsszesSkin[i-1][id] >= 1)
				{
					ColorChat(0, GREEN, "%s ^3%s ^1Kirakott egy ^4%s^1-t a piacra^4 %.2f.00^1 dollárért", C_Prefix, name, Fegyverek[i-1][0], Erteke[id]);
					kirakva[id] = 1;
				}
			}
		}
	}
	menu_destroy(menu);
	return PLUGIN_HANDLED;
}
public fvalaszt(id) {
	new szMenuTitle[ 121 ],cim[121];
	format( szMenuTitle, charsmax( szMenuTitle ), "%s \r- \dVálassz Fegyvert", Prefix);
	new menu = menu_create( szMenuTitle, "fvalaszt_h" );
	
	for(new i=0; i < MAX; i++) {
		if(OsszesSkin[i][id] > 0) {
			new Num[6];
			num_to_str(i, Num, 5);
			formatex(cim, charsmax(cim), "%s \d[\r%d DB\d]", Fegyverek[i][0], OsszesSkin[i][id]);
			menu_additem(menu, cim, Num);
		}
	}
	
	menu_setprop(menu, MPROP_EXIT, MEXIT_ALL);
	menu_display(id, menu, 0);
	
}
public fvalaszt_h(id, menu, item){
	if(item == MENU_EXIT)
	{
		menu_destroy(menu);
		return;
	}
	new data[9], szName[64];
	new access, callback;
	menu_item_getinfo(menu, item, access, data,charsmax(data), szName,charsmax(szName), callback);
	new key = str_to_num(data);
	
	kicucc[id] = key+1;
	Eladas(id);
}
public Fegyvermenu(id)
{
	new String[121];
	formatex(String, charsmax(String), "%s \r- \d[VIP] Fegyvermenü", Prefix);
	new menu = menu_create(String, "Fegyvermenu_h");
	
	menu_additem(menu, "M4A4", "1", 0);
	menu_additem(menu, "AK47", "2", 0);
	menu_additem(menu, "AWP", "3", 0);
	menu_additem(menu, "FAMAS", "4", 0);
	menu_additem(menu, "MP5", "5", 0);
	menu_additem(menu, "SCOUT", "6", 0);
	menu_additem(menu, "M3", "7", 0);
	
	menu_display(id, menu, 0);
	
	return PLUGIN_HANDLED;
}
public Fegyvermenu_h(id, menu, item){
if(item == MENU_EXIT)
{
menu_destroy(menu);
return;
}

new data[9], szName[64];
new access, callback;
menu_item_getinfo(menu, item, access, data,charsmax(data), szName,charsmax(szName), callback);
new key = str_to_num(data);

switch(key) 
	{
		case 1:
		{
			strip_user_weapons(id);
			Pisztolyok(id);
			give_item(id, "weapon_m4a1");
			give_item(id, "item_thighpack");
			give_item(id, "item_assaultsuit");
			give_item(id, "weapon_hegrenade");
			give_item(id, "weapon_flashbang");
			give_item(id, "weapon_flashbang");
			give_item(id, "weapon_smokegrenade");
			cs_set_user_bpammo(id,CSW_M4A1,200);
			cs_set_user_money(id, 0);
		}
		case 2:
		{
			strip_user_weapons(id);
			Pisztolyok(id);
			give_item(id, "weapon_ak47");
			give_item(id, "item_thighpack");
			give_item(id, "item_assaultsuit");
			give_item(id, "weapon_hegrenade");
			give_item(id, "weapon_flashbang");
			give_item(id, "weapon_flashbang");
			give_item(id, "weapon_smokegrenade");
			cs_set_user_bpammo(id,CSW_AK47,200);
			cs_set_user_money(id, 0);
		}
		case 3:
		{
			strip_user_weapons(id);
			Pisztolyok(id);
			give_item(id, "weapon_awp");
			give_item(id, "item_thighpack");
			give_item(id, "item_assaultsuit");
			give_item(id, "weapon_hegrenade");
			give_item(id, "weapon_flashbang");
			give_item(id, "weapon_flashbang");
			give_item(id, "weapon_smokegrenade");
			cs_set_user_bpammo(id,CSW_AWP,30);
			cs_set_user_money(id, 0);
		}
		case 4:
		{
			strip_user_weapons(id);
			Pisztolyok(id);
			give_item(id, "weapon_famas");
			give_item(id, "item_thighpack");
			give_item(id, "item_assaultsuit");
			give_item(id, "weapon_hegrenade");
			give_item(id, "weapon_flashbang");
			give_item(id, "weapon_flashbang");
			give_item(id, "weapon_smokegrenade");
			cs_set_user_bpammo(id,CSW_FAMAS,200);
			cs_set_user_money(id, 0);
		}
		case 5:
		{
			strip_user_weapons(id);
			Pisztolyok(id);
			give_item(id, "weapon_mp5navy");
			give_item(id, "item_thighpack");
			give_item(id, "item_assaultsuit");
			give_item(id, "weapon_hegrenade");
			give_item(id, "weapon_flashbang");
			give_item(id, "weapon_flashbang");
			give_item(id, "weapon_smokegrenade");
			cs_set_user_bpammo(id,CSW_MP5NAVY,200);
			cs_set_user_money(id, 0);
		}
		case 6:
		{
			strip_user_weapons(id);
			Pisztolyok(id);
			give_item(id, "weapon_scout");
			give_item(id, "item_thighpack");
			give_item(id, "item_assaultsuit");
			give_item(id, "weapon_hegrenade");
			give_item(id, "weapon_flashbang");
			give_item(id, "weapon_flashbang");
			give_item(id, "weapon_smokegrenade");
			cs_set_user_bpammo(id,CSW_SCOUT,200);
			cs_set_user_money(id, 0);
		}
		case 7:
		{
			strip_user_weapons(id);
			Pisztolyok(id);
			give_item(id, "weapon_m3");
			give_item(id, "item_thighpack");
			give_item(id, "item_assaultsuit");
			give_item(id, "weapon_hegrenade");
			give_item(id, "weapon_flashbang");
			give_item(id, "weapon_flashbang");
			give_item(id, "weapon_smokegrenade");
			cs_set_user_bpammo(id,CSW_M3,200);
			cs_set_user_money(id, 0);
		}
	}
}
public Pisztolyok(id)
{
	new String[121];
	formatex(String, charsmax(String), "%s \r- \d[VIP] Fegyvermenü", Prefix);
	new menu = menu_create(String, "Pisztolyok_h");
	menu_additem(menu, "DEAGLE", "1", 0);
	menu_additem(menu, "USP-S", "2", 0);
	menu_additem(menu, "GLOCK18", "3", 0);
	
	menu_display(id, menu, 0);
	
	return PLUGIN_HANDLED;
}
public Pisztolyok_h(id, menu, item){
if(item == MENU_EXIT)
{
menu_destroy(menu);
return;
}

new data[9], szName[64];
new access, callback;
menu_item_getinfo(menu, item, access, data,charsmax(data), szName,charsmax(szName), callback);
new key = str_to_num(data);

switch(key) 
	{
		case 1:
		{
			give_item(id, "weapon_knife");
			give_item(id, "weapon_deagle");
			cs_set_user_bpammo(id,CSW_DEAGLE,50);
		}
		case 2:
		{
			give_item(id, "weapon_knife");
			give_item(id, "weapon_usp");
			cs_set_user_bpammo(id,CSW_USP,50);
		}
		case 3:
		{
			give_item(id, "weapon_knife");
			give_item(id, "weapon_glock18");
			cs_set_user_bpammo(id,CSW_GLOCK18,100);
		}
	}
}
public lekeres(id) {
	new ertek, adatok[32];
	read_args(adatok, charsmax(adatok));
	remove_quotes(adatok);
	
	ertek = str_to_num(adatok);
	
	new hossz = strlen(adatok);
	
	if(hossz > 7)
	{
		client_cmd(id, "messagemode DOLLAR");
	}
	else if(ertek < 200)
	{
		ColorChat(id, GREEN, "%s ^1Nem tudsz eladni fegyvert ^4200 Dollár ^1alatt.", C_Prefix);
		Eladas(id);
	}
	else
	{
		Erteke[id] = ertek;
		Eladas(id);
	}
}
public Vasarlas(id)
{      
	new mpont[512], menu, cim[121];
	static players[32],temp[10],pnum;  
	get_players(players,pnum,"c");
	
	format(cim, charsmax(cim), "%s \r- \dVásárlás^nDollár: \r%d $", Prefix, Dollar[id]);
	menu = menu_create(cim, "vasarlas_h" );
	
	for (new i; i < pnum; i++)
	{
		if(kirakva[players[i]] == 1 && Erteke[players[i]] > 0)
		{
			for(new a=0; a < MAX; a++) {
				if(kicucc[players[i]] == a)
					formatex(mpont,256,"%s \d[Ára: \r%d $\d]", Fegyverek[a-1][0], Erteke[players[i]]);
			}
			
			num_to_str(players[i],temp,charsmax(temp));
			menu_additem(menu, mpont, temp);
		}
	}
	menu_setprop(menu, MPROP_PERPAGE, 6);
	menu_setprop(menu, MPROP_EXIT, MEXIT_ALL );
	menu_display(id, menu);
	return PLUGIN_HANDLED;
}  
public vasarlas_h(id,menu, item){
	if(item == MENU_EXIT)
	{
		menu_destroy(menu);
		return;
	}
	if(pido != 0){
		Vasarlas(id);
		return;
	}
	new data[6] ,szName[64],access,callback;
	new name[32], name2[32];
	get_user_name(id, name, charsmax(name));
	
	menu_item_getinfo(menu, item, access, data, charsmax(data), szName, charsmax(szName), callback);
	
	new player = str_to_num(data);
	get_user_name(player, name2, charsmax(name2));
	pido = 2;
	set_task(2.0, "vido");
	
	for(new i=0; i < MAX; i++) {
		if(Dollar[id] >= Erteke[player] && kicucc[player] == i && kirakva[player] == 1)
		{
			kirakva[player] = 0;
			ColorChat(0, GREEN, "%s ^3%s ^1vett egy ^4%s^1-t ^3%s^1-tól ^4%d ^1Dollárért!",C_Prefix, name, Fegyverek[i-1][0], name2, Erteke[player]);
			Dollar[player] += Erteke[player];
			Dollar[id] -= Erteke[player];
			OsszesSkin[i-1][id] ++;
			OsszesSkin[i-1][player] --;
			kicucc[player] = 0;
			Erteke[player] = 0;
		}
	}
}
public vido()
{
	pido = 0;
}
public client_disconnect(id)
{
if(!is_user_bot(id))
{
	Update(id);
}
Belepve[id] = false;
Beirtjelszot[id] = false;
Beirtjelszot1[id] = false;
Beirtfelhasznalot[id] = false;
Beirtfelhasznalot1[id] = false;

regFh[id][0] = EOS;
regJelszo[id][0] = EOS;
Felhasznalonev[id][0] = EOS;
Jelszo[id][0] = EOS;

Dollar[id] = 0.0;
Rang[id] = 0;
Oles[id] = 0;
Kulcs[id] = 0;
EXP[id] = 0.0;
SMS[id] = 0;
Masodpercek[id] = 0;
Regisztralt[id] = 0;
AutoB[id] = 0;

for(new i;i < MAX; i++)
OsszesSkin[i][id] = 0;

for(new i;i < LADA; i++)
Lada[i][id] = 0;

for(new i;i < LADA; i++)
LadaK[i][id] = 0;

copy(name[id], charsmax(name[]), "");
}
public client_putinserver(id)
{
if(!is_user_bot(id))
{
	get_user_name(id, name[id], charsmax(name));
	Load(id);
}
Gun[id] = true;
Hud[id] = true;
Belepve[id] = false;
Felhasznalonev[id] = "";
Jelszo[id] = "";
}
public plugin_cfg()
{
	g_SqlTuple = SQL_MakeDbTuple(SQLINFO[0], SQLINFO[1], SQLINFO[2], SQLINFO[3]);
	
	static Query[10048];
	new Len;
	
	Len += formatex(Query[Len], charsmax(Query), "CREATE TABLE IF NOT EXISTS `surf_ski_2`");
	Len += formatex(Query[Len], charsmax(Query)-Len, "(`Nev` varchar(32) NOT NULL, ");
	Len += formatex(Query[Len], charsmax(Query)-Len, "`Felhasznalonev` varchar(32) NOT NULL,");
	Len += formatex(Query[Len], charsmax(Query)-Len, "`Jelszo` varchar(32) NOT NULL,");
	Len += formatex(Query[Len], charsmax(Query)-Len, "`Regisztralt` int(11) NOT NULL,");
	Len += formatex(Query[Len], charsmax(Query)-Len, "`Masodpercek` int(11) NOT NULL,");
	Len += formatex(Query[Len], charsmax(Query)-Len, "`SMS` int(11) NOT NULL,");
	Len += formatex(Query[Len], charsmax(Query)-Len, "`AutoB` int(11) NOT NULL,");
	Len += formatex(Query[Len], charsmax(Query)-Len, "`Dollars` int(11) NOT NULL,");
	Len += formatex(Query[Len], charsmax(Query)-Len, "`Szint` int(11) NOT NULL,");
	Len += formatex(Query[Len], charsmax(Query)-Len, "`Oles` int(11) NOT NULL,");
	Len += formatex(Query[Len], charsmax(Query)-Len, "`DropOles` int(11) NOT NULL,");
	Len += formatex(Query[Len], charsmax(Query)-Len, "`Vip` int(11) NOT NULL,");
	Len += formatex(Query[Len], charsmax(Query)-Len, "`EXP` int(11) NOT NULL,");
	
	for(new i;i < MAX; i++)
		Len += formatex(Query[Len], charsmax(Query)-Len, "`F%d` int(11) NOT NULL,", i);
	for(new i;i < LADA; i++)
		Len += formatex(Query[Len], charsmax(Query)-Len, "`L%d` int(11) NOT NULL,", i);
	for(new i;i < 11; i++)
		Len += formatex(Query[Len], charsmax(Query)-Len, "`S%d` int(11) NOT NULL,", i);
	for(new i;i < LADA; i++)
		Len += formatex(Query[Len], charsmax(Query)-Len, "`K_%d` int(11) NOT NULL,", i);	
	
	Len += formatex(Query[Len], charsmax(Query)-Len, "`Kulcs` int(11) NOT NULL, `id` INT(11) NOT NULL AUTO_INCREMENT PRIMARY KEY)");
	
	SQL_ThreadQuery(g_SqlTuple, "createTableThread", Query);
}
public Load(id) {
	static Query[10048];
	new Data[1], Name[32];
	get_user_name(id, Name, 31);
	Data[0] = id;

	formatex(Query, charsmax(Query), "SELECT * FROM `surf_ski_2` WHERE Nev = ^"%s^";", name[id]);
	SQL_ThreadQuery(g_SqlTuple, "QuerySelectData", Query, Data, 1);
}
public QuerySelectData(FailState, Handle:Query, Error[], Errcode, Data[], DataSize, Float:Queuetime) {
	if(FailState == TQUERY_CONNECT_FAILED || FailState == TQUERY_QUERY_FAILED) {
		log_amx("%s", Error);
		return;
	}
	else {
		new id = Data[0];
 
		if(SQL_NumRows(Query) > 0) {
			Rang[id] = SQL_ReadResult(Query, SQL_FieldNameToNum(Query, "Szint"));
			Dollar[id] = SQL_ReadResult(Query, SQL_FieldNameToNum(Query, "Dollars"));
			SMS[id] = SQL_ReadResult(Query, SQL_FieldNameToNum(Query, "SMS"));
			AutoB[id] = SQL_ReadResult(Query, SQL_FieldNameToNum(Query, "AutoB"));
			Oles[id] = SQL_ReadResult(Query, SQL_FieldNameToNum(Query, "Oles"));
			D_Oles[id] = SQL_ReadResult(Query, SQL_FieldNameToNum(Query, "DropOles"));
			Vip[id] = SQL_ReadResult(Query, SQL_FieldNameToNum(Query, "Vip"));
			EXP[id] = SQL_ReadResult(Query, SQL_FieldNameToNum(Query, "EXP"));
			Masodpercek[id] = SQL_ReadResult(Query, SQL_FieldNameToNum(Query, "Masodpercek"));
			SQL_ReadResult(Query, SQL_FieldNameToNum(Query, "Felhasznalonev"), regFh[id], charsmax(regFh[]));
			SQL_ReadResult(Query, SQL_FieldNameToNum(Query, "Jelszo"), regJelszo[id], charsmax(regJelszo[]));
			Regisztralt[id] = SQL_ReadResult(Query, SQL_FieldNameToNum(Query, "Regisztralt"));
			
			for(new i;i < MAX; i++)
			{
				new String[64];
				formatex(String, charsmax(String), "F%d", i);
				OsszesSkin[i][id] = SQL_ReadResult(Query, SQL_FieldNameToNum(Query, String));
			}
			for(new i;i < LADA; i++)
			{
				new String[64];
				formatex(String, charsmax(String), "L%d", i);
				Lada[i][id] = SQL_ReadResult(Query, SQL_FieldNameToNum(Query, String));
			}
			for(new i;i < LADA; i++)
			{
				new String[64];
				formatex(String, charsmax(String), "K_%d", i);
				LadaK[i][id] = SQL_ReadResult(Query, SQL_FieldNameToNum(Query, String));
			}
			for(new i;i < 11; i++)
			{
				new String[64];
				formatex(String, charsmax(String), "S%d", i);
				Skin[i][id] = SQL_ReadResult(Query, SQL_FieldNameToNum(Query, String));
			}
			
			Kulcs[id] = SQL_ReadResult(Query, SQL_FieldNameToNum(Query, "Kulcs"));
		}
		else
		{
			Save(id);
		}
	}
}
public Save(id)
{
static Query[256];
 
formatex(Query, charsmax(Query), "INSERT INTO `surf_ski_2` (`Nev`) VALUES (^"%s^");", name[id]);
SQL_ThreadQuery(g_SqlTuple, "QuerySetData", Query);
}
public SQL_Update_Reg(id)
{
static Query[10048];
new Len;

Len += formatex(Query[Len], charsmax(Query), "UPDATE `surf_ski_2` SET Felhasznalonev = ^"%s^", ", regFh[id]);
Len += formatex(Query[Len], charsmax(Query)-Len, "Jelszo = ^"%s^", ", regJelszo[id]);
Len += formatex(Query[Len], charsmax(Query)-Len, "Regisztralt = ^"%i^" WHERE Nev = ^"%s^";", Regisztralt[id], name[id]);

SQL_ThreadQuery(g_SqlTuple, "QuerySetData", Query);
}
public Update(id)
{
	static Query[10048];
	new Len;
	
	Len += formatex(Query[Len], charsmax(Query), "UPDATE `surf_ski_2` SET Dollars = ^"%i^", ",Dollar[id]);
	Len += formatex(Query[Len], charsmax(Query)-Len, "Szint = ^"%i^", ", Rang[id]);
	Len += formatex(Query[Len], charsmax(Query)-Len, "Oles = ^"%i^", ", Oles[id]);
	Len += formatex(Query[Len], charsmax(Query)-Len, "DropOles = ^"%i^", ", D_Oles[id]);
	Len += formatex(Query[Len], charsmax(Query)-Len, "AutoB = ^"%i^", ", AutoB[id]);
	Len += formatex(Query[Len], charsmax(Query)-Len, "Masodpercek = ^"%i^", ", Masodpercek[id]+get_user_time(id));
	Len += formatex(Query[Len], charsmax(Query)-Len, "SMS = ^"%i^", ", SMS[id]);
	Len += formatex(Query[Len], charsmax(Query)-Len, "Vip = ^"%i^", ", Vip[id]-get_user_time(id));
	Len += formatex(Query[Len], charsmax(Query)-Len, "EXP = ^"%i^", ", EXP[id]);
	
	for(new i=0;i < MAX; i++)
		Len += formatex(Query[Len], charsmax(Query)-Len, "F%d = ^"%i^", ", i, OsszesSkin[i][id]);
		
	for(new i;i < LADA; i++)
		Len += formatex(Query[Len], charsmax(Query)-Len, "L%d = ^"%i^", ", i, Lada[i][id]);
		
	for(new i;i < LADA; i++)
		Len += formatex(Query[Len], charsmax(Query)-Len, "K_%d = ^"%i^", ", i, LadaK[i][id]);	
		
	for(new i;i < 11; i++)
		Len += formatex(Query[Len], charsmax(Query)-Len, "S%d = ^"%i^", ", i, Skin[i][id]);
	
	Len += formatex(Query[Len], charsmax(Query)-Len, "Kulcs = ^"%i^" WHERE Nev = ^"%s^";", Kulcs[id], name[id]);
	
	SQL_ThreadQuery(g_SqlTuple, "QuerySetData", Query);
}
public createTableThread(FailState, Handle:Query, Error[], Errcode, Data[], DataSize, Float:Queuetime) {
	if(FailState == TQUERY_CONNECT_FAILED)
		set_fail_state("[HIBA*] NEM TUDTAM CSATLAKOZNI AZ ADATBAZISHOZ!");
	else if(FailState == TQUERY_QUERY_FAILED)
		set_fail_state("Query Error");
	if(Errcode)
		log_amx("[HIBA*] HIBAT DOBTAM: %s",Error);
}
public QuerySetData(FailState, Handle:Query, Error[], Errcode, Data[], DataSize, Float:Queuetime) {
	if(FailState == TQUERY_CONNECT_FAILED || FailState == TQUERY_QUERY_FAILED) {
		log_amx("%s", Error);
		return;
	}
}
public plugin_end() {
	SQL_FreeHandle(g_SqlTuple);
}
public sayhook(id)
{
	new message[192], Name[32], none[2][32], chat[192];
	read_args(message, 191);
	remove_quotes(message);
	
	formatex(none[0], 31, ""), formatex(none[1], 31, " ");
	
	if (message[0] == '@' || message[0] == '/' || message[0] == '#' || message[0] == '!' || equal (message, ""))
		return PLUGIN_HANDLED;
	
	if(!equali(message, none[0]) && !equali(message, none[1]))
	{
		get_user_name(id, Name, 31);
		if(is_user_alive(id))
		{
			if(Regisztralt[id] == 0)
				formatex(chat, 191, "^x04[Jatekos]^x03%s^x01: %s", Name, message);
			else if(get_user_flags(id) & TULAJ && Belepve[id] == true)
				formatex(chat, 191, "^x04[Tulajdonos][%s]^3%s^x04: %s", Rangok[Rang[id]][Szint], Name, message);
			else if(get_user_flags(id) & FOADMIN && Belepve[id] == true)
				formatex(chat, 191, "^x04[Föadmin][%s]^3%s^x04: %s", Rangok[Rang[id]][Szint], Name, message);
			else if(get_user_flags(id) & ADMIN && Belepve[id] == true)
				formatex(chat, 191, "^x04[Föadmin][%s]^x03%s^x04: %s", Rangok[Rang[id]][Szint], Name, message);
			else if(get_user_flags(id) & VIP && Belepve[id] == true)
				formatex(chat, 191, "^x04[VIP][%s]^x03%s^x04: %s", Rangok[Rang[id]][Szint], Name, message);
			else if(Belepve[id] == true)
				formatex(chat, 191, "^x04[%s]^x03%s^x01: %s", Rangok[Rang[id]][Szint], Name, message);
			else if(Regisztralt[id] == 1)
				formatex(chat, 191, "^x04[Jatekos]^x03 %s^x01: %s", Name, message);
		}
		else {
			get_user_team(id, color, 9);
			if(Regisztralt[id] == 0)
				formatex(chat, 191, "^x01*Halott*^x04[Jatekos]^x03 %s^x01: %s", Name, message);
			else if(get_user_flags(id) & TULAJ && Belepve[id] == true)
				formatex(chat, 191, "^x01*Halott*^x04[Tulajdonos][%s]^3 %s^x04: %s", Rangok[Rang[id]][Szint], Name, message);
			else if(get_user_flags(id) & FOADMIN && Belepve[id] == true)
				formatex(chat, 191, "^x01*Halott*^x04[Föadmin][%s]^3 %s^x04: %s", Rangok[Rang[id]][Szint], Name, message);
			else if(get_user_flags(id) & ADMIN && Belepve[id] == true)
				formatex(chat, 191, "^x01*Halott*^x04[Admin][%s]^x03 %s^x04: %s", Rangok[Rang[id]][Szint], Name, message);
			else if(get_user_flags(id) & VIP && Belepve[id] == true)
				formatex(chat, 191, "^x01*Halott*^x04[VIP][%s]^x03 %s^x04: %s", Rangok[Rang[id]][Szint], Name, message);
			else if(Belepve[id] == true)
				formatex(chat, 191, "^x01*Halott*^x04[%s]^x03 %s^x01: %s", Rangok[Rang[id]][Szint], Name, message);
			else if(Regisztralt[id] == 1)
				formatex(chat, 191, "^x01*Halott*^x04[Halott]^x03 %s^x01: %s", Name, message);
		}
		
		
		switch(cs_get_user_team(id))
		{
			case 1: ColorChat(0, RED, chat);
			case 2: ColorChat(0, BLUE, chat);
		}
		if(cs_get_user_team(id) == CS_TEAM_SPECTATOR)
			ColorChat(0, GREY, chat);
		return PLUGIN_HANDLED;
	}
	return PLUGIN_CONTINUE;
}
public sendmessage(color[])
{
	new teamName[10];
	for(new player = 1; player < get_maxplayers(); player++)
	{
		get_user_team (player, teamName, 9);
		teamf (player, color);
		elkuldes(player, Temp);
		teamf(player, teamName);
	}
}
public teamf(player, team[])
{
	message_begin(MSG_ONE, get_user_msgid("TeamInfo"), _, player);
	write_byte(player);
	write_string(team);
	message_end();
}
public elkuldes(player, Temp[])
{
	message_begin( MSG_ONE, get_user_msgid( "SayText" ), _, player);
	write_byte( player );
	write_string( Temp );
	message_end();
}
