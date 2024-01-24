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

Provide a high-level overview of your RPG combat system template. Explain its main features and how it enhances the gaming experience.

### Custom Enum Types
Discuss the Custom Enum Types used in your system. Explain how they contribute to the flexibility and customization of the combat system. Give examples of how these enums are utilized within the template.

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
