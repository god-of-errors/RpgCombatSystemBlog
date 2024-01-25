---
layout: default
title: "RPG Battle System Blog Post"
---

# The Making of a Turn-based RPG Battle System Template

## Introduction
As a huge fan of the RPG genre, from the expansive worlds of Xenoblade Chronicles and Dragon Quest 11 to the captivating narratives of Chained Echoes and Sea of Stars, my love for RPG games has always been paramount. Naturally, this deep passion created in me a profound itch to actively contribute to a genre that has consistently sparked my imagination.

Driven by this aspiration, I went ahead tried to develop a customizable turn-based RPG battle system template. This endeavor was not only a pursuit of my personal creative ambitions but a way to create a robust framework for ther fans and developers to work on their own visions. The ensuing six weeks were dedicated to the making and refinement of this turn-based RPG battle system template.

### Who am I?
My name is Alfonso Santana Samper. During my time at BUAS, in the Game Programming course, I undertook the challenge of creating a turn-based RPG battle system template. This project was developed utilizing the Bee engine, a platform designed by teachers specifically for students. In this log, I will delve into some of the concepts and implementations that went into the template.

## RPG Battle System Template
### Overview
The RPG battle system template I developed is designed to integrate into an ECS (Entity Component System). This system defines turn-based mechanics, with a particular focus on turn order, which is determined by a unit's speed. It also handles selecting the order of units and identifying the current unit currently in play.

The template is adaptable and allows users to define battle data, ensuring the presence of units on both sides, which is an indispensable prerequisite for any functional battle system. After all, a battle system comes to life only when both sides are present. Although it was initially built with ImGui UI dependency, the template has been separated from it to ensure adaptability to any UI provided.

The progression within the battle system is triggered by calling one of the five essential functions (or six if you want to count Win and Lose battle as two different ones):

1. **Start Battle**: Initiates the battle and sets the stage for the upcoming fight.
2. **Player Turn**: Allows the user to choose what the current Unit will do, whether it be using a skill or utilizing an item.
3. **Enemy Turn**: This function allows enemies to make any strategic decisions during their turn.
4. **WinBattle/LoseBattle**: These virtual functions happen at end of a battle, enabling customization for events such as leveling up prompts or displaying a game-over screen.
5. **Reset**: A straight-forward function that resets the template, making it ready for another battle.

The SetNextTurn() function is the backbone of our RPG system template, responsible for managing turn order, handling the current unit, and updating buffs and debuffs countdowns. It plays a crucial role in controlling the flow of battle rounds. This function is called at the end of StartBattle, EnemyTurn, and PlayerTurn to move the system to the next turn or round. It's intentionally omitted from the other three scenarios as they occur after the turn progression.

Here's a simplified breakdown of the code:

```c++
void RpgBattleSystem::SetNextTurn()
{
    // Check if it's the start of a new round
    if (m_CurrentUnitIndex == 0)
    {
        UpdateStartOfRound();
        std::sort(m_TurnOrder.begin(), m_TurnOrder.end(), m_CurrentBattleData.GetCompareSpeed());
    }
    
    const int currentUnitIndex = m_CurrentUnitIndex % m_TurnOrder.size();
    m_CurrentUnit = m_TurnOrder[currentUnitIndex];
    
    /// (Turn Order Display code)

    m_CurrentUnitIndex++;

    m_CurrentUnit->HandleBuffAndDebuffExpiration();

    UpdateStartOfTurn();

    // Check if this completes a round, if yes, then reset
    if (m_CurrentUnitIndex >= m_TurnOrder.size())
    {
        m_CurrentUnitIndex = 0;
        // Indicate the end of the round or perform any round-specific actions here
    }
    
    if (m_CurrentUnit->IsDead())
    {
        SetNextTurn();
    }
}
```
In a nutshell, the function checks if it's the start of a new round, sorts the turn order, sets the current unit, updates buffs and debuffs, and resets the turn index at the end of each round. If the current unit is dead, it recursively calls itself to find the next living unit.

## Custom Enum Types
To address the challenge of static and inflexible enums when creating types such as skill types, item types, and element types, I introduced a dynamic solution: Custom Enum Types. This custom class facilitates flexibility by allowing users to add types after the initial definition. The process is simple, with each new type creation generating a unique ID, stored in a std::unordered_map, and subsequently used to retrieve the type's ID.

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
The BaseSkill class serves as an important component within the RPG system, providing units with the ability to attack and interact with others. It contains essential attributes such as a name, the MP needed for the attack, power, and skill type. Additionally, it can include an elemental attribute and buffs/debuffs that it applies when used, if need be.

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

Moreover, the template allows for the creation of game-specific skills. For example, Chained Echoes contains a game-specific thing with its overdrive mechanic. This updates every time a skill is used and depending on what stage it's on, the MP consume more or else magic point (in that game it's called TP though). This is demonstrated in the following example of a Chained Echoes Buff/Debuff skill:

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
The BaseItem class serves as a versatile foundation for items within the RPG system, providing units, primarily the player, the ability to choose from multiple items and utilize them in and out of battle. Whether it's consumable items used for attacks, healing, or buffs, or collectible items used for crafting, the BaseItem class covers the fundamental aspects that any item should have: a name and the item's quantity. However, its power lies in how diverse its inherited classes can be.

One such example is the ConsumableItem, which contains a skill allowing it to be used in battle with that skill's effect. This approach allows for different consumable item effects, such as creating a Pokeball item:

```c++
bee::rpg::Pokeball::Pokeball(std::string name, const int amount, int ballValue): ConsumableItem(std::move(name), amount)
{
    CustomEnumTypes itemEnumTypes = bee::Engine.ECS().GetSystem<MonsterCollectorBattleSystem>().GetCustomTypes("itemType");
    ItemTypeId = itemEnumTypes.GetId("PokeBall");

    ItemSkill = new PokeballSkill(ballValue);
}
```

### Inventory
Managing items can be done easily through the use of an inventory. The inventory is designed to handle any BaseItem or inherited item while managing cleanup when an item gets used. For instance, if an item is used, the inventory erases the item if there are none left and returns the skill that the item contains, if any. The AddItem and GetItem functions mirror their counterparts in the Unit class for consistency, as they perform similar tasks.

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
The GetConsumableItems function in the MonsterCollectorBag example demonstrates an exception to getting all items, providing flexibility in defining consumable items:

```c++
std::vector<size_t> bee::rpg::MonsterCollectorBag::GetIndicesOfConsumableItems() const
{
    CustomEnumTypes itemTypes = Engine.ECS().GetSystem<MonsterCollectorBattleSystem>().GetCustomTypes("itemType");
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

1. **Advanced AI Behaviors:**
Enhancing the AI's decision-making process is a main priority at the moment. The current template relies on random skill and target selection, but incorporating more sophisticated AI behaviors, such as Utility AI, can elevate the enemies' challenge level and introduce dynamic decision-making.

2. **Character Progression System:**
This would involve the implementation of customizable skill trees, character classes, and a robust level-up system. This would help add depth and personalization to the player's journey.

3. **Quest and Story Incorporation:**
Expanding the template to integrate with a broader RPG framework, including incorporating quest triggers based on battle outcomes, enabling character interactions mid-battle, etc.

4. **Enhanced Accessibility:**
Future iterations could focus on visual and audio enhancements, introducing animations, special effects, and soundtracks to create a more immersive and captivating atmosphere during battles.

5. **Visual and Audio Enhancements:**
Future iterations could focus on visual and audio enhancements, introducing animations, special effects, and soundtracks to create a more immersive and captivating atmosphere during battles.

These future directions aim to not only refine the existing template but also broaden its appeal to a wider audience, fostering creativity and innovation in the realm of RPG game development.

## Conclusion

In conclusion, the RPG Combat System Template reflects my dedication to the RPG genre and my commitment to offering a valuable tool for game developers. Through the amalgamation of turn-based mechanics and dynamic customization, the template enables creators to craft their own RPG worlds. The progression from dynamic Custom Enum Types to the foundational components of BaseUnit and BaseSkill highlights the template's versatility and efficacy. Looking forward, the template will evolve to incorporate advanced AI behaviors, implement a comprehensive character progression system, integrate intricate narratives, enhance accessibility, and elevate the gaming experience with visual and audio enhancements. This template serves as a robust tool not only for constructing a Turn-Based RPG battle system but also as a platform for others to generate, refine, and build their creative ideas upon.

<img src="Logo BUas_RGB.jpg" alt="BUAS Logo" width="1500" height="400">