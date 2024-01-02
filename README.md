https://community.bistudio.com/wiki/DayZ:Doors_on_buildings

DayZ:Doors on buildings
Jump to navigationJump to search


Contents
1	Overview
2	Model
3	Model config
4	Game config
5	Special cases
6	Sample
Overview
This page describes how to create and configure doors for custom building models.

Requirements:

Work-folder / Work-drive with custom mod files (folder and config structure).
Object Builder (from DayZ Tools package) with Buldozer (model preview) setup.
Ability to binarize and pack custom mod files.
Model
Interactive doors require the following to be set in the model:

Named selection that is going to be animated in all respective LODs (resolution, geometry, view geometry, fire geometry).
Memory LOD vertices to define an axis of rotation/translation, where the sound should be played and where the widget itself should be displayed.
Named property 'class' with value 'house' in the geometry LOD.
dayz md doors model lods.png

Additionally, there are some things to consider:

We strongly recommend modelling your doors closed by default and always animate them in chosen direction to be open (we plan to make doors open-able in both directions).
Almost all of the doors in DayZ are 120x220 cm.
Model config
Any doors that are going to be animated require a model config. This config is used for many things when creating content for the game and because this is base on the RV technology, a lot of information available at Model Config applies to DayZ. The information from the model config is used by Object Builder and Buldozer and also later baked into the p3d during the binarization. This config follows very similar syntax to the normal game configs (config.cpp), except it should always be named model.cfg. It supports #include of hpp and also macros.

Start by creating a file called model.cfg near the particular model/s in which you want to have a doors. model.cfg does not necessarily need to be near the particular model (can be higher in the folder structure), it is just a matter of how you prefer your data to be organized.

Model config consist of two parts:

CfgSkeletons - where the animated bones are defined.
CfgModels - where the animations itself are defined.
Take a look on a following example, where model.cfg is defined for a building with 2 doors:

class CfgSkeletons
{
  class Default
  {
    isDiscrete = 1;
    skeletonInherit = "";
    skeletonBones[] = {};
  };
  class Skeleton_2door: Default
  {
    skeletonInherit = "Default";
    skeletonBones[] =
    {
      "door1","",
      "door2",""
    };
  };
};
class CfgModels
{
  class Default
  {
    sectionsInherit = "";
    sections[] = {};
    skeletonName = "";
  };
  class yourmodelname: Default
  {
    skeletonName = "Skeleton_2door";
    class Animations
    {
      class Door1
      {
        type = "rotation";
        selection = "door1";
        source = "door1";
        axis = "door1_axis";
        memory = 1;
        minValue = 0;
        maxValue = 1;
        angle0 = 0;
        angle1 = 1.400000;
      };
      class Door2
      {
        type = "rotation";
        selection = "door2";
        source = "door2";
        axis = "door2_axis";
        memory = 1;
        minValue = 0;
        maxValue = 1;
        angle0 = 0;
        angle1 = -1.400000;
      };
    };
  };
};
Here an explanation of all relevant parameters from the example model config:

skeletonBones - Contains all the named selections for doors that you want to have animated.
type - Commonly used value is 'rotation', but other values - animation types can be used.
selection - Which named selection should be animated.
source - Provides link with game config (see game config part of this guide below).
axis - Named selections for the vertex/vertices defining the axis.
angle1 - Angle in radians defining the angle of rotation (how wide doors should open).
Keep in mind that the class names in CfgModels entries have to correspond with the actual model names to get model linked with the model config.

After you are done with the model config, you can use Object Builder with Buldozer to verify that the work you did on the model config and in the model itself is correct and behaves as intended. By default, use [ and ] keys to cycle through the available animation sources and ; ' or mouse wheel up/down to advance/recede the animation state.

Game config
Game config class name has to be land_modelname in order to be linked correctly with the model. Please keep in mind that adding a class=house named property and config class requires your terrain to be re-binarized (paths to models in wrp will be replaced with game config class).

Following example game config is for the custom building that was configured by the model and model.cfg above. This example should be saved into config.cpp file in the same folder with the building ideally.

class CfgPatches
{
  class yourcustombuilding
  {
    units[] = {};
    weapons[] = {};
    requiredVersion = 0.1;
    requiredAddons[] = {"DZ_Data"};
    author = "yourname";
    name = "addonname";
    url = "websitelink";
  };
};
class CfgVehicles
{
  class HouseNoDestruct;
  class land_modelname: HouseNoDestruct
  {
    model = "\path\to\your\model\file.p3d";
    class Doors
    {
      class Door1
      {
        displayName = "door 1";
        component = "Door1";
        soundPos = "door1_action";
        animPeriod = 1;
        initPhase = 0;
        initOpened = 0.5;
        soundOpen = "sound open";
        soundClose = "sound close";
        soundLocked = "sound locked";
        soundOpenABit = "sound openabit";
      };
      class Door2
      {
        displayName = "door 2";
        component = "Door2";
        soundPos = "door2_action";
        animPeriod = 1;
        initPhase = 0;
        initOpened = 0.5;
        soundOpen = "sound open";
        soundClose = "sound close";
        soundLocked = "sound locked";
        soundOpenABit = "sound openabit";
      };
    };
    class DamageSystem
    {
      class GlobalHealth
      {
        class Health
        {
          hitpoints = 1000;
        };
      };
      class GlobalArmor
      {
        class Projectile
        {
          class Health { damage = 0; };
          class Blood { damage = 0; };
          class Shock { damage = 0; };
        };
        class Melee
        {
          class Health { damage = 0; };
          class Blood { damage = 0; };
          class Shock { damage = 0; };
        };
      };
      class DamageZones
      {
        class Door1
        {
          class Health
          {
            hitpoints = 1000;
            transferToGlobalCoef = 0;
          };
          componentNames[] = {"door1"};
          fatalInjuryCoef = -1;
          class ArmorType
          {
            class Projectile
            {
              class Health { damage = 2; };
              class Blood { damage = 0; };
              class Shock { damage = 0; };
            };
            class Melee
            {
              class Health { damage = 2.5; };
              class Blood { damage = 0; };
              class Shock { damage = 0; };
            };
          };
        };
        class Door2
        {
          class Health
          {
            hitpoints = 1000;
            transferToGlobalCoef = 0;
          };
          componentNames[] = {"door2"};
          fatalInjuryCoef = -1;
          class ArmorType
          {
            class Projectile
            {
              class Health { damage = 2; };
              class Blood { damage = 0; };
              class Shock { damage = 0; };
            };
            class Melee
            {
              class Health { damage = 2.5; };
              class Blood { damage = 0; };
              class Shock { damage = 0; };
            };
          };
        };
      };
    };
  };
};
What you can see in the example?

CfgPatches is required to include in any of your game configs in order to get your config loaded.
CfgVehicles is part, where you define the actual game config for your building.
All buildings in DayZ are child objects of HouseNoDestruct class, thus we need to define it first.
Class name of the building itself should correspond only to land_modelname to ensure the model<->config link works.
Model parameter is path to the building p3d file.
Class Doors contains definitions for all doors available on the building.
Each class name in class Doors should correspond to the source parameter in the model.cfg.
displayName - currently not used.
component - is the selection that you use for this specific doors-component in the view geometry.
soundPos - is the named selection assigned to vertex in model memory LOD, where the door sounds are played.
animPeriod - speed of the animation.
initPhase - initial value for the animation (you can try to animate it in Buldozer to see which is 0 and which is 1).
initOpened - defines at which animation value doors are more likely to be found in the world.
soundOpen - CfgActionSounds sound class for opening doors, look into DZ\sounds\hpp\config.cpp to get a full list of available sound sets.
soundClose - CfgActionSounds sound class for closing doors, look into DZ\sounds\hpp\config.cpp to get a full list of available sound sets.
soundLocked - CfgActionSounds sound class for locked door sounds (when player tries to open a locked doors), look into DZ\sounds\hpp\config.cpp to get a full list of available sound sets.
soundOpenABit - CfgActionSounds sound class for locked door sounds (when player breaks locked doors), look into DZ\sounds\hpp\config.cpp to get a full list of available sound sets.
DamageSystem - This is quite a complex one, but all you need to keep in mind is to add a subclass per each class in already mentioned class Doors, specify proper component name (from model fire geometry) in componentNames[] array and also damage particular doors would receive from a projectile and health (you can look into the structures configs which values are used on different doors).
After you are done with setting up game config part, make sure to pack everything properly and your building and all doors should be ready to use in the game. Please keep in mind that rebuilt terrain without an updated navmesh can cause issues with AI walking through doors.

Special cases
Double doors

This type of doors is often used in the game as it makes the navigation in environment a bit simpler. Several things need to be done in the model and config:
In the model, configure doors individually except the case of Memory LOD, where the action point should be shared between the two doors (one named selection and one vertex) and the no-suffix named selection ("doorX") should cover both door handles. Additionally, view and fire geometry also requires a new named selection covering both wings.
In the model config, also define everything like it would be single doors case, but make sure that for both wings of the double doors, set the same source param in CfgModels part (lets say "doors34"). Also dont forget that the angle1 should be one positive and one negative.
In the game config, make sure to define just one class in Doors class, with its name corresponding to the source parameter you specified in the model.cfg earlier. Same applies for the DamageZones class.
Shifting doors

To have doors shifted along the axis (instead rotated), make sure to use translation type instead of rotation in the model config (CfgModels entries).
Doors getting ignored by ray-casting (bounding sphere problem)

By default, bounding sphere contains has its radius set in a way it contains whole object. But, modelling doors closed by default can bring problems with features animating outside of the bounding sphere. This can and in most does affect ray-casting for actions (non-interact-able doors from certain angles) or ballistic not registering doors at all. In order to avoid that, you can create a named selection in memory LOD, covering the larger area that the building covers when it has doors in open state and link it using a config parameter (in your game config class) called bounding (bounding = "selection_name";), which can override bounding sphere size.
