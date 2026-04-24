---
title: Extensions for my 2D Player Controller
date: 2026-04-24 02:00:00 +0200
lang: en
categories: [Studies, Modules, 4FSC0PD002]
tags: [2D Unity, Player Controller, Unity]
---

# Documentation: Refined 2D Player Controller

## 1. Project Overview
* **Project Name:** [2DLearningSAE](https://git.zetacron.de/Yuuto/sae/src/branch/master/4FSC0PD002/2DLearningSAE)
* **Objective:** Implementing "Game Feel" mechanics to enhance the platforming experience.

---

## 2. Old Player Controller
The Player Controller i am using for the refinementations is the one from my [2DLearningSAE](https://git.zetacron.de/Yuuto/sae/src/branch/master/4FSC0PD002/2DLearningSAE) project.

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

For now there is now Documentation about this, maybe i'll make one someday!

---

## 3. Implemented Mechanics

### Mechanic 1: Coyote Time
* **Inspiration:** *Super Mario*
* **Functionality:** Provides the player with a Short-term window (e.g., 0.1s - 0.15s) to jump even after leaving the edge of a platform.
* **Impact on Game Feel:** Prevents player frustration during frame-perfect or tight jumps.

### Mechanic 2: Jump Buffering
* **Inspiration:** *Hollow Knight*
* **Functionality:** If the jump button is pressed shortly before hitting the ground, the system buffers the input and triggers the jump automatically upon landing.
* **Impact on Game Feel:** Enables smoother movement chains and prevents "dropped" inputs during fast-paced gameplay.

### Mechanic 3: Wall Slide & Wall Jump
* **Inspiration:** *Celeste*
* **Functionality:** While in the air and touching a wall, falling speed is reduced (Slide). Pressing the jump button against a wall triggers a force pushing the player upward and away from the surface.
* **Impact on Game Feel:** Expands level design possibilities with vertical sections and significantly increases character mobility.

---

## 4. Technical Implementation & Parameters
The following parameters were set in the Inspector.

| Parameter | Description | Recommended Value |
| :--- | :--- | :--- |
| `ParameterName` | Description | Value |

---

## 5. Interaction of Mechanics
During testing, special attention was paid to how the mechanics interact:

---

## 6. Approaches & Solutions

---

## 7. Bugs & Troubleshooting
