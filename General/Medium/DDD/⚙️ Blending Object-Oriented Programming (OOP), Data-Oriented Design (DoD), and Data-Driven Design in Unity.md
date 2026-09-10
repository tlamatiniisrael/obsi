
_A scalable approach to gameplay systems that won’t collapse under feature creep_

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/0*wju7dtNlo29xMx8E)

As developers, we often treat **Object-Oriented Programming (OOP)**, **Data-Oriented Design (DoD)**, and **data-driven programming** as separate — even competing — approaches.

In reality, combining them unlocks:

- ✅ Clean architecture
- ⚡ High performance
- 🧠 Flexibility and scalability for live projects

This article walks through how to structure **data-driven gameplay abilities and systems in Unity**, using OOP for clarity, DoD for performance, and a **data-driven approach** to avoid hard-coding yourself into a corner.

## 🧠 Step 1: OOP & DoD — Two Philosophies, One Engine

## 🧩 OOP — Encapsulation and Behavior

OOP bundles data and behavior together into objects. In Unity, that’s your bread and butter: `MonoBehaviour` components.

public class Player : MonoBehaviour  
{  
    [SerializeField] private float speed = 5f;  
    [SerializeField] private int health = 100;  
  
    void Update()  
    {  
        transform.Translate(Vector3.forward * speed * Time.deltaTime);  
    }  
}

Great for small projects. But as your game grows, you end up with many components holding both state and logic. This **tight coupling** creates friction when you want to scale or optimize.

## 🧠 DoD — Data First, Behavior Separate

**Data-Oriented Design** focuses on how data is used and laid out in memory — which improves cache performance and scalability.

You don’t need DOTS to apply DoD. Simply:

- Separate data from logic
- Use lightweight data containers (structs, ScriptableObjects, or JSON files)
- Process data in bulk when possible

[CreateAssetMenu(menuName = "Configs/PlayerData")]  
public class PlayerData : ScriptableObject  
{  
    public float speed;  
    public int health;  
}

This `ScriptableObject` holds only data — no behavior.

public class PlayerController : MonoBehaviour  
{  
    [SerializeField] private PlayerData data;  
  
    private int currentHealth;  
  
    void Start() => currentHealth = data.health;  
  
    void Update()  
    {  
        transform.Translate(Vector3.forward * data.speed * Time.deltaTime);  
    }  
}

Here, **OOP (PlayerController)** uses **DoD (PlayerData)**.

## 🔍 Has vs Is — The Separation of Concerns

When we combine these two styles in Unity, the key is to think in terms of **“Has” vs “Is.”**

- **“Has”** describes what data a class or component owns.  
    Example: _“This entity has movement data, health data, and attack data.”_
- **“Is”** describes what behavior a class or component performs.  
    Example: _“This component is responsible for moving the entity.”_

By separating the two, we can build more flexible and efficient code — the core of blending OOP and DoD.

👉 **Data** can live in:

- 🧾 `ScriptableObject` assets (ideal for shared, immutable config)
- 🧱 Structs (fast, lightweight, local data)
- 🌐 JSON files (externalized or network-driven configuration)

## 🧠 Step 2: The Problem with Logic-Driven Code

> _“One ability, one method, one hard-coded number — and then… requirements change.”_

This is how many systems start:

- One simple damage ability.
- Then knockback.
- Then scaling with level.
- Then edge cases.
- Then 40,000 lines of brittle code nobody wants to touch.

This is what happens when **behavior lives in your code** instead of in **data**.

## 🚀 Step 3: Data-Driven Design

Instead of hardcoding abilities, **define them as data** and let the system execute based on configuration.

## 🔸 Define an Ability as Data

[CreateAssetMenu(menuName = "Abilities/Ability Data")]  
public class AbilityData : ScriptableObject  
{  
    public string label;  
  
    [SerializeReference] public List<AbilityEffect> effects = new();  
}

## 🔸 Define Effects as Executable Data

[System.Serializable]  
public abstract class AbilityEffect  
{  
    public abstract void Execute(GameObject caster, GameObject target);  
}  
  
[System.Serializable]  
public class DamageEffect : AbilityEffect  
{  
    public int amount;  
  
    public override void Execute(GameObject caster, GameObject target)  
    {  
        var health = target.GetComponent<Health>();  
        if (health != null)  
        {  
            health.TakeDamage(amount);  
            Debug.Log($"{target.name} took {amount} damage!");  
        }  
    }  
}  
  
[System.Serializable]  
public class KnockbackEffect : AbilityEffect  
{  
    public float force;  
  
    public override void Execute(GameObject caster, GameObject target)  
    {  
        var rb = target.GetComponent<Rigidbody>();  
        if (rb != null)  
        {  
            Vector3 dir = (target.transform.position - caster.transform.position).normalized;  
            rb.AddForce(dir * force, ForceMode.Impulse);  
            Debug.Log($"{target.name} knocked back with force {force}");  
        }  
    }  
}

Now each effect is **just data and one small piece of logic**.

## 🔸 Executing the Ability

public class AbilityExecutor : MonoBehaviour  
{  
    [SerializeField] private AbilityData ability;  
    [SerializeField] private GameObject target;  
  
    void Update()  
    {  
        if (Keyboard.current.spaceKey.wasPressedThisFrame)  
            ExecuteAbility(target);  
    }  
  
    public void ExecuteAbility(GameObject t)  
    {  
        foreach (var effect in ability.effects)  
            effect.Execute(gameObject, t);  
    }  
}

✅ Adding new ability types = no if/else spaghetti  
✅ Designers can configure behavior in the Inspector  
✅ Code stays lean and testable

## 🧱 Step 4: Power-Up the Editor with Property Drawers

Unity’s built-in Inspector doesn’t display `SerializeReference` polymorphic lists nicely by default.

👉 Enter **Custom Property Drawers**.

Here’s a simplified custom drawer that:

- Lists available `AbilityEffect` subclasses
- Lets you switch between them dynamically
- Draws their serialized fields cleanly

// This goes in an editor folder  
  
using UnityEditor;  
using UnityEngine;  
using System;  
using System.Linq;  
using System.Collections.Generic;  
  
[CustomPropertyDrawer(typeof(AbilityEffect), true)]  
public class AbilityEffectDrawer : PropertyDrawer  
{  
    private static Dictionary<string, Type> effectTypes;  
  
    public override float GetPropertyHeight(SerializedProperty property, GUIContent label)  
    {  
        return EditorGUI.GetPropertyHeight(property, true);  
    }  
  
    public override void OnGUI(Rect position, SerializedProperty property, GUIContent label)  
    {  
        if (effectTypes == null)  
            CacheEffectTypes();  
  
        EditorGUI.BeginProperty(position, label, property);  
  
        string typeName = property.managedReferenceFullTypename.Split(' ').Last();  
        string currentType = string.IsNullOrEmpty(typeName) ? "<None>" : typeName;  
  
        var dropdownRect = new Rect(position.x, position.y, position.width, EditorGUIUtility.singleLineHeight);  
  
        if (EditorGUI.DropdownButton(dropdownRect, new GUIContent(currentType), FocusType.Keyboard))  
        {  
            var menu = new GenericMenu();  
            foreach (var kvp in effectTypes)  
            {  
                bool isSelected = kvp.Key == currentType;  
                menu.AddItem(new GUIContent(kvp.Key), isSelected, () =>  
                {  
                    property.managedReferenceValue = Activator.CreateInstance(kvp.Value);  
                    property.serializedObject.ApplyModifiedProperties();  
                });  
            }  
            menu.ShowAsContext();  
        }  
  
        position.y += EditorGUIUtility.singleLineHeight + EditorGUIUtility.standardVerticalSpacing;  
  
        if (!string.IsNullOrEmpty(property.managedReferenceFullTypename))  
            EditorGUI.PropertyField(position, property, true);  
  
        EditorGUI.EndProperty();  
    }  
  
    private void CacheEffectTypes()  
    {  
        effectTypes = AppDomain.CurrentDomain.GetAssemblies()  
            .SelectMany(a => a.GetTypes())  
            .Where(t => typeof(AbilityEffect).IsAssignableFrom(t) && !t.IsAbstract)  
            .ToDictionary(t => t.Name, t => t);  
    }  
}

🎯 **Result:** You can add multiple effects to a single ability via a clean dropdown — all without external tools.

🫡 **Shoutout to** Odin Inspector — it provides this and many other features _out of the box_, so if you use it, there’s no need to write a custom property drawer like this one.

> _📝_ Footnote: _Odin Inspector is entirely optional but can be a huge time-saver for teams that want advanced inspector tooling without maintaining custom editor code._

## ⚡ Step 5: Keep Data Immutable Where It Makes Sense

ScriptableObjects are shared assets. If multiple enemies reference the same `EnemyData` and you mutate it at runtime, all enemies change.

**Best practice:**

- Keep static config (speed, base damage, max health) in ScriptableObjects
- Store mutable state (current health, timers, cooldowns) in components or structs.

## 🧠 Step 6: Thinking in Bulk (DoD Mindset)

Even outside of DOTS:

- Process data in predictable layouts
- Avoid scattered `GetComponent` in hot loops
- Structure updates around batched data

foreach (var enemy in enemies)  
{  
    enemy.ApplyEffect(fireballData);  
}

This mindset gives you performance benefits **without migrating to ECS**.

## 📊 When to Use OOP, DoD, or Blended

## 🧠 TL;DR

- 🧩 **OOP:** Keep your gameplay logic structured and understandable.
- ⚡ **DoD:** Separate data from behavior and structure data for efficiency.
- 🧠 **Data-Driven Design:** Make abilities and systems extensible through data, not hard-coded logic.
- 🧰 **Custom Drawers:** Enable designers to build gameplay without engineers adding if/else branches.
- 🛡️ Immutable ScriptableObjects for config, mutable structs/fields for runtime state.

## 🏁 Final Thoughts

By **blending OOP + DoD + Data-Driven** approaches:

- 🧹 Your codebase stays clean
- 🧱 Your systems scale gracefully
- 🧪 New content can be added without fear of breaking old code

This hybrid mindset is a **modern Unity architecture superpower**.  
It’s what helps teams build systems that survive scope creep, sequels, and live updates.

✨ **Shoutout:** Odin Inspector is optional but can be a huge time-saver for teams that want advanced inspector tooling without maintaining custom editor code.

Unity

Unity Game Development

Game Development

Programming

Design Patterns

4

[

![James Lafritz](https://miro.medium.com/v2/resize:fill:48:48/0*vwgB44nYWyuU6614)



](https://medium.com/@ktmarine1999?source=---post_author_info--0315d467a02b---------------------------------------)

[

## Written by James Lafritz

](https://medium.com/@ktmarine1999?source=---post_author_info--0315d467a02b---------------------------------------)

[266 followers](https://medium.com/@ktmarine1999/followers?source=---post_author_info--0315d467a02b---------------------------------------)

·[11 following](https://medium.com/@ktmarine1999/following?source=---post_author_info--0315d467a02b---------------------------------------)

Excited about changing my hobby into a new carer with GameDevHQ course.

Follow