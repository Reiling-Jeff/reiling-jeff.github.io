---
title: Erweiterungen für meinen 2D Player Controller
date: 2026-04-24 02:00:00 +0200
lang: de
categories: [Studium, Module, 4FSC0PD002]
tags: [2D Unity, Player Controller, Unity]
---

# Dokumentation: Erweiterter 2D Player Controller

## 1. Projektübersicht
* **Projektname:** [2DLearningSAE](https://git.zetacron.de/Yuuto/sae/src/branch/master/4FSC0PD002/2DLearningSAE)
* **Ziel:** Implementierung von Game-Feel Mechaniken zur Verbesserung des Platforming-Erlebnisses, inspiriert durch moderne Klassiker wie *Celeste*.

---

## 2. Alter Palyer Controller
Der Player Controller an dem ich die verbesserungen vornehme, ist der aus dem [2DLearningSAE](https://git.zetacron.de/Yuuto/sae/src/branch/master/4FSC0PD002/2DLearningSAE) Projekt.

```cs
using UnityEngine;
using UnityEngine.InputSystem;

public class PlayerController : MonoBehaviour, InputSystem_Actions.IPlayerActions
{
    [Header("Movement")]
    [SerializeField] private float moveSpeed = 5f;
    [SerializeField] private float jumpForce = 10f;

    [Header("Ground Check")]
    [SerializeField] private Transform groundCheck;
    [SerializeField] private float groundCheckRadius = 0.2f;
    [SerializeField] private LayerMask groundLayer;

    private Rigidbody2D m_rb;
    private SpriteRenderer m_sr;
    private float m_moveInput;
    private bool m_isGrounded;

    [SerializeField]
    private LayerMask voidLayer;

    [SerializeField]
    private Animator animator;

    private InputSystem_Actions m_actions;

    private void Awake()
    {
        m_actions = new InputSystem_Actions();
    }

    private void Start()
    {
        m_rb = GetComponent<Rigidbody2D>();
        m_sr = GetComponent<SpriteRenderer>();
    }

    private void OnEnable()
    {
        m_actions.Player.Jump.started += OnJump;
        m_actions.Player.Move.started += OnMove;
        m_actions.Player.Move.performed += OnMove;
        m_actions.Player.Move.canceled += OnMove;
        m_actions.Player.Enable();
    }

    private void OnDisable()
    {
        m_actions.Player.Disable();
        m_actions.Player.RemoveCallbacks(this);
    }

    private void Update()
    {
        m_sr.flipX = m_moveInput == -1 || (m_sr.flipX == true && m_moveInput == 0);
        m_isGrounded = Physics2D.OverlapCircle(groundCheck.position, groundCheckRadius, groundLayer);
        SetAnimations();
    }

    private void SetAnimations()
    {
        animator.SetFloat("y", m_rb.linearVelocity.y);
        animator.SetBool("isGrounded", m_isGrounded);
        animator.SetBool("isWalking", m_moveInput != 0);
    }

    private void FixedUpdate()
    {
        m_rb.linearVelocity = new Vector2(m_moveInput * moveSpeed, m_rb.linearVelocity.y);
    }

    public void OnJump(InputAction.CallbackContext context)
    {
        if (m_isGrounded)
            m_rb.linearVelocity = new Vector2(m_rb.linearVelocity.x, jumpForce);
    }

    public void OnMove(InputAction.CallbackContext context)
    {
        m_moveInput = Mathf.Round(context.ReadValue<Vector2>().x);
    }
}

```

Dazu gibt es zum jetzigem stand keine Dokumentation. Vielleicht mache ich mal eine!

---
## 3. Implementierte Mechaniken

### Mechanik 1: Coyote Time
* **Inspiration:** *Super Mario*
* **Funktionsweise:** Dem Spieler wird ein kurzes Zeitfenster (z. B. 0.1s - 0.15s) gewährt, in dem er noch springen kann, nachdem er die Kante einer Plattform verlassen hat.
* **Einfluss auf das Spielgefühl:** Verhindert, dass der User bei knappen Sprüngen Frustriert wird.

### Mechanik 2: Jump Buffering
* **Inspiration:** *Hollow Knight*
* **Funktionsweise:** Wenn die Sprungtaste kurz vor der Landung gedrückt wird, speichert das System diesen Input und führt den Sprung automatisch im Moment des Bodenkontakts aus.
* **Einfluss auf das Spielgefühl:** Erlaubt flüssigere Kettenreaktionen und verhindert "verschluckte" Inputs bei schnellen Bewegungen.

### Mechanik 3: Wall Slide & Wall Jump
* **Inspiration:** *Celeste*
* **Funktionsweise:** Beim Kontakt mit einer Wand in der Luft wird die Fallgeschwindigkeit reduziert (Slide). Ein Sprunginput an der Wand löst einen Kraftstoß nach oben und in die entgegengesetzte Richtung aus.
* **Einfluss auf das Spielgefühl:** Erweitert das Leveldesign um vertikale Passagen und erhöht die Mobilität des Charakters.

---

## 4. Technische Umsetzung & Parameter
### Coyote Time

| Parameter | Beschreibung | Empfohlener Wert |
| :--- | :--- | :--- |
| `coyoteTimeDuration` | Wie lange der coyote timer sein soll in Sekunden | 0.15 |

Zuerst müssen wir uns überlegen welche Variablen ein Coyote Time bruacht. 
Die einmal einen Counter, und eine Duration. In diesem fall ist die Duration ein [SerializeField]("https://docs.unity3d.com/6000.3/Documentation/ScriptReference/SerializeField.html")

```cs
[Header("Coyote Time")]
[SerializeField] private float coyoteTimeDuration = 0.15f;
private float coyoteTimeCounter; 
```

In der Update() methode setzen wir dann einfach den Counter = Duration, wenn der Spieler den Ground berührt. wenn er das nicht tut, ziehen wir [Time.deltaTime;](https://docs.unity3d.com/6000.3/Documentation/ScriptReference/Time-deltaTime.html) vom Counter ab.

```cs
if (m_isGrounded)
  coyoteTimeCounter = coyoteTimeDuration;
else
  coyoteTimeCounter -= Time.deltaTime;
```

Statt in der OnJump() methode zu prüfen ob der Spieler den boden berühert, prüfen wir ab jetzt ob der Counter kleiner oder gleich 0 ist. wenn, returnen wir. 
Hat den simpelen grund, wenn der Spieler Auf dem Boden steht, dann ist der Counter immer auf den wert von der Duration gesetzt. Wenn der spieler fällt, dann zählz er runter, also wird er irgendwann unter den wert 0 fallen, wenn das passiert, ist der Timer abgelaufen und der Spieler kann nicht springen.

```cs
if (m_coyoteTimeCounter <= 0f) return;
m_rb.linearVelocity = new Vector2(m_rb.linearVelocity.x, jumpForce);
            
m_coyoteTimeCounter = 0f;
```
Wichtig: am ende muss der Counter auf 0 gesetzt werden, um einen double jump zu vermeiden.

---

## 5. Kombination der Mechaniken
Im Testlauf wurde besonders auf das Zusammenspiel geachtet:

---

## 6. Lösungswege

---

## 7. Bugs & Lösungen
