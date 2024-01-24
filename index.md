---
layout: default
title: "RPG system Blog Post"
---

# RPG System Blog Post

## Introductiom
As an avid enthusiast of the RPG genre, my passion has been ignited by titles like Xenoblade Chronicles, Dragon Quest 11, Chained Echoes, Bravely Default, and Sea of Stars. The allure of immersive worlds, captivating stories, and strategic turn-based combat has always fascinated me. So, it was only a matter of time before the urge to create something within this realm took over.

In this journey, I embarked on the ambitious task of crafting a customizable turn-based RPG battle system template—a foundation upon which others could build their own epic adventures. Over the course of the next six weeks, I delved into the intricate details, fueled by a desire to encapsulate the essence of what makes RPGs so enthralling. Join me on this creative odyssey as I unveil the intricacies of the system, from its inception to the intricacies of its core components.

## RPG Combat System Template
### Overview
The RPG Combat System Template I built is designed to function seamlessly as part of an Entity Component System (ECS). It serves as the bedrock for turn-based mechanics, defining critical elements such as the turn order based on a unit's speed and the logic for choosing the order of units in battle. Central to its functionality is the ability to set battle data, crucial for initiating encounters with units on both sides. After all, a battle system is only as robust as the adversaries it pits against each other.

Originally, the template relied on ImGui UI dependency, but I later undertook the task of decoupling it to ensure adaptability to any UI framework. This strategic separation allows the project to evolve dynamically, triggered only when one of the six essential functions is called upon:

1. StartBattle: Initiates the battle.
2. PlayerTurn: Enables the player to decide their actions, choosing between skills or items.
3. EnemyTurn: Mirrors the player's turn but for enemies, allowing them to make decisions.
4. WinBattle/LoseBattle: Left virtual to accommodate specific actions triggered upon victory or defeat, such as prompts for leveling up or game over screens.
5. Reset: Straightforwardly resets the template, preparing it for another engaging battle.

This template's flexibility extends further through the incorporation of virtual functions, allowing users to modify critical aspects effortlessly. These include altering win and lose conditions, opening avenues for diverse combat encounters—from defending a single party member to time-sensitive scenarios where players must defeat enemies before impending disaster strikes. The Enemy AI, a pivotal aspect, remains customizable, accommodating distinct decision-making paradigms, ranging from random attacks to intricate strategic planning.

Users can also dictate the events at the start of each turn or round, introducing narrative elements or dynamic mechanics like weather-induced damage. The template's adaptability not only makes it a robust foundation for turn-based RPGs but also empowers creators to infuse their unique storytelling and gameplay mechanics seamlessly. With its modular structure, this system beckons game developers to embark on creative journeys, shaping their distinct worlds within the realm of RPG excellence.

## Custom Enum Types
The journey towards a customizable RPG combat system wouldn't be complete without addressing the inherent rigidity of traditional enums. The need for flexibility arose prominently when dealing with dynamic elements like skill types, item types, and elemental attributes, where predefined enums struggled to adapt to future additions by users. The solution? Enter the realm of adaptability with a custom Enum class designed to evolve and expand based on user-defined requirements.

### Implementation
The crux of this innovation lies in the creation of a versatile CustomEnumTypes class. Here's a glimpse into how it is implemented within the RPG Combat System Template:

```C++
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
The CustomEnumTypes class allows for the dynamic addition of new types at runtime, paving the way for a more adaptable system. Each time a new type is introduced, it receives a unique ID and is seamlessly integrated into the existing framework.

### Utilization
This newfound flexibility proves invaluable when comparing IDs, as demonstrated in the RPG Combat System Template:

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

This seamless integration of custom enum types enhances the template's responsiveness to diverse scenarios, making it a versatile tool for developers seeking unique outcomes based on different battle states.

### Application in a Pokemon Combat System
Extending the template's functionality, the custom enum types become instrumental in defining new types for a Pokemon-inspired combat system:

```c++
AddNewType("statType","SpecialAttack");
AddNewType("statType","SpecialDefense");

AddNewType("elementType","Dragon");
AddNewType("elementType","Electric");
AddNewType("elementType","Psychic");
// ... and more

AddNewType("itemType","PokeBall");
```

This flexibility empowers users to define their own types tailored to specific gaming experiences, whether it be introducing new stat attributes, elemental affinities, or unique item categories within the RPG framework.

In essence, the Custom Enum Types serve as the dynamic backbone of the RPG Combat System Template, breaking free from the constraints of static enums and fostering a creative environment for developers to shape their virtual worlds with unprecedented freedom.

## BaseUnit
Purpose
Introduce the BaseUnit class and its role in your RPG combat system. Explain how it represents characters or entities within the game.

Attributes and Methods
Detail the essential attributes and methods that the BaseUnit class includes. Discuss how these elements contribute to the overall functionality of the combat system.

## BaseSkill
Functionality
Explain the purpose of the BaseSkill class and how it handles various character abilities and actions during combat. Highlight any unique features that set your system apart.

Implementation Example
Provide an example or two to showcase how BaseSkill can be implemented within your RPG combat system. Illustrate how it interacts with BaseUnit and impacts the gameplay.

## Base Item, Inventory, and Base Equipment
Interconnected Components
Explain how these three components—Base Item, Inventory, and Base Equipment—are interconnected. Discuss how they collectively manage in-game items, character inventories, and equipment.

Item Customization
Highlight any customization options you've incorporated into the items, inventory, and equipment system. This could include different types of items, rarity levels, and equipment slots.

## Conclusion
Summarize the key points discussed in your blog post. Reiterate the unique aspects of your RPG combat system template and how each component contributes to a dynamic and engaging gaming experience.

## Call to Action
Encourage readers to try out your RPG combat system template, provide feedback, or share their thoughts. Include links to any relevant documentation or resources.

## Future Development
