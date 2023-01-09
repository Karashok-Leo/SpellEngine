![Title](.github/title.png)

<div align="center">

<a href="">![Java 17](https://img.shields.io/badge/Java%2017-ee9258?logo=coffeescript&logoColor=ffffff&labelColor=606060&style=flat-square)</a>
<a href="">![Environment: Client & Server](https://img.shields.io/badge/environment-Client%20&%20Server-1976d2?style=flat-square)</a>
<a href="">[![Discord](https://img.shields.io/discord/973561601519149057.svg?label=&logo=discord&logoColor=ffffff&color=7389D8&labelColor=6A7EC2&style=flat-square)](https://discord.gg/KN9b3pjFTM)</a>

</div>

# 🪄️ Features

Data driven API
- 🗡️ Spells can be assigned to any weapon (data driven)
- 🔮 Spells deal damage based on [Spell Power](https://github.com/ZsoltMolnarrr/SpellPower) entity attributes
- ✍️ Spells defined in JSON format
- ⚙️ Spells have a set of different mechanical behaviours:
    - Cast options: duration, mode of release (charged or channeled)
    - Targeting mode: Area, Beam, Cursor, Projectile
    - Impact actions: Damage, Heal, StatusEffect
    - Cost: exhaust (hunger), item (runes), cooldown (time), durability, consume effects

Fancy audio and visuals
- 🔈 Spells have sound effects: at the start of casting, while casting, at release, at impact
- ✨ Spells have particle effects (any particle can be referenced by id), and the engine offers its custom set magical of particles
- 🎨 Custom Item/Block models can be used for Spell Projectiles and Status Effects
- 🤸 Custom player animations can be played at different stages of spell casting

In game features
- 🔧 Spell selection and casting is visible on the HUD (fully player configurable)
- 😌 QoL features included (such as automatic spell cast release)
- ⛓️ Bind spells to eligible weapons

# 🔧 Configuration

### Fabric

Client side settings can be accessed via the [Mod Menu](https://github.com/TerraformersMC/ModMenu).

### Forge

Client side settings can be accessed in Main Menu > Mods > Spell Engien > Config.

### Server

**Server side** configuration can be found in the `config` directory, after running the game with the mod installed.

# 🔨 Using it as a mod developer

❗️ DOCUMENTATION IS INCOMPLETE!

## Installation

Add this mod as dependency into your build.gradle file.

Install dependencies:
- Spell Power
- Player Animator

## ⭐️ Creating a spell

Create a new file at `resources/data/MOD_ID/spells/SPELL_ID.json`.

Write the content of the JSON file to match the structure of the [Spell](common/src/main/java/net/spell_engine/api/spell/Spell.java) class. Your JSON will be parsed into a Spell instance.

Spells are automatically registered. 

### Resources

#### Sound
You can register a sound effects to be used for different stages of the spell casting. Alternatively you can use generic magic school related sound effects provided by this mod.

#### Icon
Place your spell icon to the following location: `resources/assets/MOD_ID/textures/spell/SPELL_ID.png`

#### Name and description

Add name and description entries into your translation file.
```
"spell.MOD_ID.SPELL_ID.name": "Spell Name",
"spell.MOD_ID.SPELL_ID.description": "Shoots and epic energy ball that does {damage} lightning damage",
```
The description supports multiple string [token](common/src/main/java/net/spell_engine/client/gui/SpellTooltip.java) to display dynamic information.

#### Custom models

Spells can render custom models, using Item/Block format (can be created using BlockBench).
Place your model to: `resources/assets/MOD_ID/models/item/MY_PROJECTILE_NAME.json`
Register your own model, like this:

```
CustomModels.registerModelIds(List.of(
    new Identifier(MOD_ID, "MY_PROJECTILE_NAME")
));
```

##### Projectiles

Custom model projectiles can be used by just referencing the registered projectile model.

##### Projectiles

Custom models can be used to be rendered over entities affected by custom status effects.

Create a renderer, implementing `CustomModelStatusEffect.Renderer`, and register it:
```
`CustomModelStatusEffect.register(MyStatusEffects.myEffectInstance, new MyRenderer());`
```
Example (Frost Shield, renders a big block around to player):
```
public class FrostShieldRenderer implements CustomModelStatusEffect.Renderer {
    public static final Identifier modelId_base = new Identifier(WizardsMod.ID, "frost_shield_base");
    private static final RenderLayer BASE_RENDER_LAYER = RenderLayer.getTranslucentMovingBlock();
    @Override
    public void renderEffect(int amplifier, LivingEntity livingEntity, float delta, MatrixStack matrixStack, VertexConsumerProvider vertexConsumers, int light) {
        float yOffset = 0.51F; // y + 0.01 to avoid Y fighting
        matrixStack.push();
        matrixStack.translate(0, yOffset, 0); // y + 0.01 to avoid Y fighting
        CustomModels.render(BASE_RENDER_LAYER, MinecraftClient.getInstance().getItemRenderer(), modelId_base,
                matrixStack, vertexConsumers, light, livingEntity.getId());
        matrixStack.pop();
    }
}
```

## 🪄 Assigning spells to items

Assign zero, one or more spells to an item, by creating a JSON file at: `resources/data/MOD_ID/item_spell_assignment/ITEM_ID.json`.

Your JSON file will be parsed into a [Spell Container](common/src/main/java/net/spell_engine/api/spell/SpellContainer.java).

Example wand (one spell assigned, no more can be added)
```
{
  "school": "FIRE",
  "max_spell_count": 1,
  "spell_ids": [ "MOD_ID:SPELL_ID" ]
}
```

Example staff (zero spell assigned, 3 can be added)
```
{
  "school": "FIRE",
  "max_spell_count": 3,
  "spell_ids": [ ]
}
```

When an item has an assigned Spell Container, it will be eligible for Spell Power enchantments.