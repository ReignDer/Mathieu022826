---
title: Blind Assassin
published: 2026-04-03
description: 'A FPS game where you are blind'
image: './BLIND_ASSASSIN_LOGO.png'
tags: [Game Development, C#, Unity]
category: 'Works'
draft: false 
lang: ''
---
# Gameplay
<video width="50%" height = "auto" controls>
  <source src="/Mathieu022826/Files/Download.mp4" type="video/mp4">
</video>

> Video Source: [Source](https://www.tiktok.com/@xx_blind_assassin_xx/video/7619949719309307157)

# Role
- Main Game Developer and Project Lead
- Developed and Updated the Full Screen Post-Process Scanner Shader inspired by the Odrabek Scanner in Death Stranding which was recreated in Unity by [Sichen Liu](https://80.lv/articles/recreating-death-stranding-odradek-terrain-scanner-in-unity)
    - Updated the code to be compatible with Unity 6.3 new Renderer Pipline
- Developed Core Gameplay Mechanics ensuring an accessible and responsive user experience.
    - Health System
    - Damage System
    - Weapon Logic
    - Black Environment for blindness
    - Scanner Shader
    - Win/Lose Conditions
- Architected and programmed enemy AI behaviours using the GOAP system to provide dynamic gameplay loop.
    - Data-Oriented GOAP using Scriptable Objects to simplify configuration in code.
        ```yaml
        protected virtual void SetupBeliefs()
        {
            beliefs = new Dictionary<string, AgentBeliefs>();
            factory = new BeliefFactory(this, beliefs);
            foreach (var belief in GOAPConfig.dynamicBeliefs)
            {
                factory.AddBelief(belief.beliefName, () =>
                {
                    bool result = belief.evaluator.Evaluate(blackboard);
                    return belief.IsInversed ? !result : result;
                });
            }
            foreach (var loc in blackboard.locationMap)
            {
                factory.AddLocationBelief(loc.conditionKey, 3f, loc.value);
            }
        }
        protected virtual void SetupActions()
        {
            actions = new HashSet<AgentAction>();

            foreach (var data in GOAPConfig.actionConditions)
            {
                var builder = new AgentAction.Builder(data.actionName)
                    .WithStrategy(data.strategy.CreateStrategy(this))
                    .WithCost(data.cost);

                foreach (var preCon in data.preConditions)
                {
                    if (beliefs.ContainsKey(preCon))
                        builder.AddPrecondition(beliefs[preCon]);
                }

                foreach (var effects in data.effects)
                {
                    if (beliefs.ContainsKey(effects))
                        builder.AddEffects(beliefs[effects]);
                }

                actions.Add(builder.Build());
            }
        }
        protected virtual void SetupGoals()
        {
            goals = new HashSet<AgentGoal>();

            foreach (var goal in GOAPConfig.goalConditions)
            {
                goals.Add(new AgentGoal.Builder(goal.goalName)
                    .WithPriority(goal.priorty)
                    .WithDesiredEffects(beliefs[goal.desiredEffects])
                    .Build());
            }
        }
        ```

- Engineered animation system using Unity's Animator to bridge the gap between art and code to streamline character transitions.
    - Quick Set-Up which sets the Hash code of the animation.
        ```yaml
        public abstract class AnimationController : MonoBehaviour
        {
            private void Awake()
            {
                animator = GetComponentInChildren<Animator>();
                SetLocomotionClip();
                SetAttackClip();
                SetAimClip();
                SetSpeedHashClip();
                SetReloadClip();
                SetDeathClip();
                SetGetHitClip();
            }
        }
        ```
    - Plays the Animation based on the clip's speed
        ```yaml
        public abstract class AnimationController : MonoBehaviour
        {
            private void PlayAnimationUsingTimer(int clipHash)
            {
                timer = new CountdownTimer(GetAnimationLength(clipHash));
                timer.OnTimerStart += () => animator.CrossFade(clipHash, k_crossFadeDuration);
                timer.OnTimerEnd += () => animator.CrossFade(locomotionClip, k_crossFadeDuration);
                timer.Start();
            }
            public float GetAnimationLength(int hash)
            {
                if (clipLengths.TryGetValue(hash, out float length)) return length;

                foreach (AnimationClip animationClip in animator.runtimeAnimatorController.animationClips)
                {
                    Debug.Log($"Name: {animationClip.name}");
                    if(Animator.StringToHash(animationClip.name) == hash)
                    {
                        clipLengths[hash] = animationClip.length;
                        return animationClip.length;
                    }
                }

                return -1;
            }
        }
        ```

- Executed QA testing, identifying and resolving critical bugs to polish the final build.
- Composed and produced original music of the game to enhance immersion.
    <audio controls style="width: 100%;">
    <source src="/Mathieu022826/Files/Blind_Assassin_Theme.wav" type="audio/wav">
    Your browser does not support the audio element.
    </audio>
- Integrated SFX and musical triggers to ensure seamless audio-visual synchronization.
- Implementing VFX to code to enhance visual appeal.