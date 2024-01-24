---
layout: default
title: "RPG system Blog Post"
---

# RPG System Blog Post

## Introductiom
As an avid enthusiast of the RPG genre and its diverse sub-genres, ranging from the expansive worlds of Xenoblade Chronicles and Dragon Quest 11 to the captivating narratives of Chained Echoes, and Sea of Stars, my passion for RPG games has always been at the forefront. It was only natural that I harbored a deep-seated desire to contribute to this genre that has consistently fueled my imagination.

In pursuit of this aspiration, I embarked on a journey to create a customizable turn-based RPG battle system template. This endeavor aimed not only to fulfill my personal creative drive but also to provide a framework that fellow enthusiasts and developers could use bring their visions to life. The follwing six weeks were dedicated to crafting and refining this RPG system template.

## RPG Battle System Template
### Overview
The RPG battle system template, I developed is designed to integrate into an ECS (Entity Component System). This system defines the turn-based mechanics, with a particular focus on turn order which is determined by a unit's speed. It also handles selecting the order of units and identifying the current unit currently in play.

The template is adaptable and allows users to define battle data, ensuring the presence of units on both sides, which is an indispensable pre-requisite for any functional battle system, after all, a battle system comes to life only when at both sides are present. Although it was initially built with ImGui UI dependency, the template has been separated form it to ensure adaptability to any UI provided.

The progression within the battle system is triggered by calling one of the six essential functions:

1. **Start Battle**: Initiates the battle and sets the stage for the upcoming fight.
2. **Player Turn**: Allows the user to choose what the current Unit will do, whether it be using a skill or utilizing an item.
3. **Enemy Turn**: This function allows enemies to make any strategic decisions during their turn.
4. **WinBattle/LoseBattle**: These virtual functions happen at end of a battle, enabling customization for events such as leveling up prompts or displaying a game-over screen.
5. **Reset**: A straight-forward function that resets the template, making it ready for another battle.

## Custom Enum Types
To address the challenge of static and inflexible enums when creating types such as skill types, item types, and element types, I introduced a dynamic solution: Custom Enum Types. This custom class facilitates flexibility by allowing users to add types after initial definition. The process is simple, with each new type creation generating a unique ID, stored in a std::unordered_map, and subsequently used to retrieve the type's ID.

Here's an example of how the statTypes enum was created in the template:

```c++
rpg::CustomEnumTypes statTypes;

statTypes.Create("CurrentHp");
statTypes.Create("CurrentMp");
statTypes.Create("MaxHp");
statTypes.Create("MaxMp");
statTypes.Create("Attack");
statTypes.Create("Defense");
statTypes.Create("Speed");
statTypes.Create("Evasion");

SetNewCustomTypes("statType", statTypes);
```
This dynamic approach extends to comparing IDs, as demonstrated in the battle state identification example:

```c++
if (chainedEchoes.GetBattleStateID() == chainedEchoesBattleState.GetId("Win"))
{
    ImGui::Begin("Battle Dialogue HUD");
    ImGui::Text("You won!");
    ImGui::End();

    chainedEchoes.WinBattle();
}
else if (chainedEchoes.GetBattleStateID() == chainedEchoesBattleState.GetId("Lose"))
{
    ImGui::Begin("Battle Dialogue HUD");
    ImGui::Text("You Lost!");
    ImGui::End();

    chainedEchoes.LoseBattle();
}
```

Additionally, this dynamic flexibility is showcased in defining new types for a Pokémon combat system based on this template:

```c++
AddNewType("statType","SpecialAttack");
AddNewType("statType","SpecialDefense");

AddNewType("elementType","Dragon");
AddNewType("elementType","Electric");
AddNewType("elementType","Psychic");
// ... and more

AddNewType("itemType","PokeBall");
```

This adaptable Custom Enum Types approach ensures that my template remains open-ended, allowing users to expand and change the system to their specific needs.

## BaseUnit
The BaseUnit class stands as another core component of my template, addressing the essential need for units within the RPG system. The concept behind this class is to encapsulate the fundamental elements that every RPG unit requires, those being a name, level stats, equipment, skills, an elemental attribute, and the ability to have buffs/debuffs applied.

Stats and skills are predefined upon unit creation, offering the flexibility for users to set and modify these attributes as needed. The AddSkill and GetSkill functions have been improved with template code to ensure that the skills being added to the unit's skill list are inherited from BaseSkill. This not only streamlines the skill addition process but also provides user assistance when retrieving a skill, ensuring that the returned skill is of the inherited type rather than the base one, including new additions, if any.

Here's an snippet of code showcasing the AddSkill and GetSkill functions:

```c++
template<typename T, std::enable_if_t<std::is_base_of_v<BaseSkill, T>, bool> = true>
auto AddSkill(const T& skillToBeAdded)
{
    SkillList.emplace_back(new T(skillToBeAdded));
}

template<typename T, std::enable_if_t<std::is_base_of_v<BaseSkill, T>, bool> = true>
T* GetSkill(const size_t index)
{
    return dynamic_cast<T*>(SkillList[index]);
}
```
This dynamic and user-friendly approach to the BaseUnit class ensures that it aligns with the overarching goal of creating a flexible and customizable RPG combat system template.

## BaseSkill
The BaseSkill class serves as a important component within the RPG system, providing units with the ability to attack and interact with others. It contains essential attributes such as a name, the MP needed for the attack, power, and skill type. Additionally, it can include an elemental attribute and buffs/debuffs that it applies when used, if need be.

The primary function of note is the Use function, where, after receiving a user and a target, the skill executes its defined action. The true power of the BaseSkill class lies in its versatility, allowing developers the freedom to define various actions when inherited. This flexibility enables the creation of skills that can dynamically adapt to different scenarios, such as a healing skill that, when used on an undead enemy, deals damage, or an attacking skill that summons a mech to attack for you.

Here's an example of a healing skill's Use function:

```c++
void bee::rpg::HealSkill::Use(const std::shared_ptr<BaseUnit>& user, const std::shared_ptr<BaseUnit>& target)
{
    // ... (Initialization and setup)

    // Heal HP
    if (HealType == HealTypes::Hp || HealType == HealTypes::Both) {
        amount = IsPercentage ? Power * (target->GetModifiedStat(statEnumTypes.GetId("CurrentHp"))) : Power;
        target->ChangeBaseStat(statEnumTypes.GetId("CurrentHp"), amount);
        std::cout << "Healed " << target->Name << " for " << amount << " HP." << '\n';
    }

    // Heal MP
    if (HealType == HealTypes::Mp || HealType == HealTypes::Both) {
        amount = IsPercentage ? Power * (target->GetModifiedStat(statEnumTypes.GetId("CurrentMp"))) : Power;
        target->ChangeBaseStat(statEnumTypes.GetId("CurrentMp"), amount);
        std::cout << "Healed " << target->Name << " for " << amount << " MP." << '\n';
    }

    // Apply Buffs/Debuffs
    for (const auto& buffOrDebuff : BuffOrDebuffListToBeApplied)
    {
        target->ApplyBuff(buffOrDebuff);
    }
}
```

Moreover, the template allows for the creation of game-specific skills, for example, Chained Echoes contains a game specific thign with it's overdrive mechanic. This updates everytime a skill is used and depending on what stage it's on, the MP consume more or else magic point (in that game it's called TP tho). This is demonstrated in the following example of a Chained Echoes Buff/Debuff skill:

```c++
void bee::rpg::CEBuffOrDebuffSkill::Use(const std::shared_ptr<BaseUnit>& user, const std::shared_ptr<BaseUnit>& target)
{
    // ... (Initialization and setup)

    // Update game-specific Overdrive mechanic
    bee::Engine.ECS().GetSystem<ChainedEchoesBattleSystem>().UpdateOverdrive(GetSkillType());

    // Consume MP based on game-specific rules
    const auto [OverdriveStage, ConsumeMp, TakeDamage, DealDamage] = bee::Engine.ECS().GetSystem<ChainedEchoesBattleSystem>().GetOverdriveInfo();
    const int totalMpCost = static_cast<int>(-(static_cast<float>(MpCost) * ConsumeMp));
    user->ChangeBaseStat(statEnumTypes.GetId("CurrentMp"), totalMpCost);

    // Apply Buffs/Debuffs based on game-specific conditions
    if (SelfOnly)
    {
        for (const auto& buffOrDebuff : BuffOrDebuffListToBeApplied)
        {
            user->ApplyBuff(buffOrDebuff);
        }
    }
    else
    {
        for (const auto& buffOrDebuff : BuffOrDebuffListToBeApplied)
        {
            target->ApplyBuff(buffOrDebuff);
        }
    }
}
```

## Base Item, Inventory, and Equipment
### Base Item & Inherited Classes
The BaseItem class stands as a versatile foundation for items within the RPG system, giving units, mainly the player, the ability to choose from multiple items and utilize them in and out of battle. Whether it's consumable items used for attacks, healing, or buffs, or collectible items used for crafting, the BaseItem class covers the fundamental aspects that any item should have: a name and the item's quantity. But of course, its power lies in how diverse its inherited classes can be.

One such example is the ConsumableItem, which contains a skill allowing it to be used in battle with that skill's effect. This approach allows for different consumable item effects, such as creating a Pokeball item:

```c++
bee::rpg::Pokeball::Pokeball(std::string name, const int amount, int ballValue): ConsumableItem(std::move(name), amount)
{
    CustomEnumTypes itemEnumTypes = bee::Engine.ECS().GetSystem<PokemonBattleSystem>().GetCustomTypes("itemType");
    ItemTypeId = itemEnumTypes.GetId("PokeBall");

    ItemSkill = new PokeballSkill(ballValue);
}
```

### Inventory
Managing items can be done easily through the use of an inventory. The inventory is designed to handle any BaseItem or inherited item while managing cleanup when an item gets used. For instance, if an item is used, the inventory erases the item if there are none left and returns the skill that the item contains, if any. The AddItem and GetItem functions mirror their counterparts in the Unit class for consistency, since they do similar things.

Snippet of the Inventory header:

```c++
template <typename T, std::enable_if_t<std::is_base_of_v<BaseItem, T>, bool>  = true>
void AddItem(const T& newItem)
{
    // ... (Add item logic)
}

template <typename T, std::enable_if_t<std::is_base_of_v<BaseItem, T>, bool>  = true>
T* GetItem(size_t index)
{
    // ... (Get item logic)
}
```
The GetConsumableItems function in the Pokemon Bag example demonstrates an exception to getting all items, providing flexibility in defining consumable items:

```c++
std::vector<size_t> bee::rpg::PokemonBag::GetIndicesOfConsumableItems() const
{
    CustomEnumTypes itemTypes = Engine.ECS().GetSystem<PokemonBattleSystem>().GetCustomTypes("itemType");
    std::vector<size_t> indices;
    size_t index = 0;
    for (const auto& item : m_Items) {
        if (item->ItemTypeId == itemTypes.GetId("Consumable") ||
            item->ItemTypeId == itemTypes.GetId("PokeBall"))
        {
            indices.push_back(index);
        }
        ++index;
    }
    return indices;
}
```
### Equipment
The BaseEquipment class expands upon the concept of the BaseItem, introducing the ability to change stats when equipped. The addition of the StatChange struct enhances the item class, allowing developers to define what stats change and by how much when equipping the equipment. The EquipmentManager class further elevates the combat system by keeping track of currently equipped equipment on a user.

```c++
class BaseEquipment : public BaseItem
{
public:
    BaseEquipment(const std::string& itemName, const int itemQuantity, const std::vector<StatChange>& statType);

    std::vector<StatChange> GetStatChanges();

private:
    std::vector<StatChange> StatTypeChanges;
};

class EquipmentManager
{
public:
    std::vector<std::shared_ptr<BaseEquipment>> Equipments;

    void AddEquipment(const std::shared_ptr<BaseEquipment>& equipmentToBeAdded);
    
    void RemoveEquipment(size_t position);
    void RemoveEquipment(const std::shared_ptr<BaseEquipment>& equipmentToBeRemoved);

    std::shared_ptr<bee::rpg::BaseEquipment> GetEquipment(size_t position);

    [[nodiscard]] int GetWeaponStatModifier(int statId) const;
private:
    std::unordered_map<int, float> m_WeaponModifier;
};
```

This design opens avenues for additional layers of depth in the combat system, allowing developers to create unique equipment features, such as providing buffs at the start of fights, or implementing familiarity mechanics. The GetWeaponStatModifier function in EquipmentManager exemplifies how the equipment can be leveraged to modify specific stats.

## Future Directions
While the current RPG Combat System Template lays a solid foundation, there are several avenues for improvement and expansion in the future:

1. Advanced AI Behaviors:
Enhancing the AI's decision-making process is a main priority at the moment. The current template relies on random skill and target selection, but incorporating more sophisticated AI behaviors, such as Utility AI, can elevate the enemies' challenge level and introduce dynamic decision-making.

2. Character Progression System:
This would involve the implementation of customizable skill trees, character classes, and a robust level-up system. This would help add depth and personalization to the player's journey.

3. Quest and Story Incorporation:
Expanding the template to integrate with a broader RPG framework, including incorporating quest triggers based on battle outcomes, enabling character interactions mid-battle, etc...

4. Enhanced Accessibility:
Making the template more user-friendly for individuals beyond programmers is a key consideration. Exploring ways to provide a more intuitive interface or incorporating visual tools would increase access to the template, allowing a broader audience to use the template.

5. Visual and Audio Enhancements:
Future iterations could focus on visual and audio enhancements, introducing animations, special effects, and soundtracks to create a more immersive and captivating atmosphere during battles.

These future directions aim to not only refine the existing template but also broaden its appeal to a wider audience, fostering creativity and innovation in the realm of RPG game development.

## Conclusion

In conclusion, the RPG Combat System Template embodies my passion for the RPG genre and my commitment to providing a valuable resource for game developers. By combining turn-based mechanics with dynamic customization, the template empowers creators to shape their own RPG worlds. The journey from dynamic Custom Enum Types to the core components of BaseUnit and BaseSkill underscores the template's adaptability and effectiveness. As the template evolves, it will embrace advanced AI behaviors, implement a comprehensive character progression system, weave intricate narratives, enhance accessibility, and elevate the gaming experience through visual and audio enhancements. This template can be used as a tool ot not only build a Turn-Based RPG battle system, but to also provide a means to for others to propel their ideas off of and expands upon them.