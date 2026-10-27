<!--
本文件由脚本生成（英文），不要手改；新增实机样本后重新生成。
- 数据：URA（UmamusumeResponseAnalyzer 官方版）`save-response-for-debug` 保存的 `single_mode_festa-*` 请求 / 响应包，
  6 局完整育成（5 局お任せ + 1 局手动），同一卡组；另读 master.mdb 的 `support_card_data` 与 `single_mode_15_*`。
- 方法：每条规则对全部 home 画面（游戏下发的预测字段）或全部动作（前后状态差）逐条检验，给出命中数、反例与样例。
- 原始包含账号标识，未公开；报告只含游戏主数据 id 与数值，生成时已做敏感值泄漏检查。
-->

# Festa (scenario 15) intake §5: answers from captured careers

Data: 6 complete careers, 474 home screens, 2459 actions. Careers: 26-10-26 17-34-07-752, 26-10-26 18-09-09-457, 26-10-27 12-54-07-171, 26-10-27 13-36-46-079, 26-10-27 14-27-06-768, 26-10-27 18-35-17-036.
master.mdb sha256 5025d7cf8a448d6e. Packet folders: 2026-10-26, 2026-10-27-official.
Ids: target 1-6 = deck positions, 101+ = scenario NPCs, 1000+ = act leads by chara id. act_type 1-5 pairs with training 101/105/102/103/106 (speed/stamina/power/guts/wiz).

| # | Question | Status | Answer |
|---|---|---|---|
| 1 | gauge 友情 +2 的范围 | confirmed | Only a lead in friendship training (its card type = this training, bond >= 80) gives +2. Every other act lead among the partners gives +1, and leads of the same act add up. The sum is capped at the act's free gauge (need - gauge). A failed training gives no gauge. |
| 2 | 休息 / 非目标比赛 +1 加哪条 | confirmed | Rest and outing add +1 to one act: among acts whose gauge is not full, the one with the lowest level, then the lowest gauge, then the lowest act_type. A race gives the same +1 to that act, and on some turns the game predicts more gauge for a race, sometimes for other acts as well. The source of the extra race gauge is not identified. |
| 3 | 演目稽古是否吃卡 buff / 彩圈 | inferred | Yes for the normal-training part. A 演目稽古 command (5101-5105) carries two parameter lists: home_info params that change with who stands in it, the same as a normal training, plus festa params (the act effect, e.g. +10 to every stat). The applied gain is their sum. |
| 4 | 多演目同时亮起的交互 | confirmed | Several acts can be full at once. Using one leaves the others full, and a full act never expires: it waits until its 演目稽古 is chosen. |
| 5 | 合宿延长量 | open | Not observed. Every career had camp on turns 13-16, 37-40 and 61-64 (summer_camp_remain_turn_num 4 -> 1). Races entered during camp did not move these windows, but none was confirmed to be a goal race, so the extension rule is still untested. |
| 6 | 友人补位是否分身 | open | A deck card can stand in two or three commands in the same turn. In normal trainings only target 1 did so in these captures. When 演目稽古 commands are on offer, every act lead (targets 1-5) shows up in several of them. The cause (card skill or scenario rule) is not identified, and the scenario friend card (target 6) never doubled. |
| 7 | 同名双人头 | confirmed | Deduplicated. When a deck card is the same character as an act's fixed lead (single_mode_15_act_member), no second head appears: the deck card takes over that act. All captured careers used one deck, so this rests on one character (1024). |
| 8 | 教え転成是否保留基础效果 | inferred | Partly answered. After 転成 the material advice leaves the list and the new advice records it in fusion_advice_id (e.g. 601 <- 101). fusion_effect rows are keyed by the material id, so the new advice carries its own advice_effect plus fusion_effect[material]. Whether the material's own advice_effect still applies was not measured. |
| 9 | 伝受 / 発表会精确回合 | confirmed | 進捗発表会 (check_point) runs at the end of turns 24, 48 and 68, after that turn's own action (training, race or event). 転成 (fusion_advice) follows the turn-24 and turn-48 発表会, then a 伝受 (attach_advice) whose response advances to turn 25 / 49. The other 伝受 are at the start of turn 3 and at the end of turns 12, 36 and 60, again advancing into the summer camp turns 13, 37 and 61. The turn-68 発表会 is followed by festa_live (本番) in the same turn. Notation below: action before->after turn. |
| 10 | finals act 效果 | inferred | Observed with every 発表会 大成功 only. The turn-68 発表会 itself gives nothing; the event after 本番 (festa_live) gives +7 to every stat, vital +30 and skill pt +400, and the turn moves to 69. One career got +15 on guts and wiz and vital +23 instead, so part of the reward depends on state that was not isolated. |
| 11 | 進捗発表会未达标惩罚 | open | Not observed. Every 発表会 in the captures ended with result_type 2 and every act at or above the schedule's need_act_level, so there is no failed or partial case yet. |
| 12 | 组卡 ≥5 种是否含友人 | open | Not testable from these captures: every career used the same deck. |

<a id="q1"></a>

## 1. gauge 友情 +2 的范围 (confirmed)

Only a lead in friendship training (its card type = this training, bond >= 80) gives +2. Every other act lead among the partners gives +1, and leads of the same act add up. The sum is capped at the act's free gauge (need - gauge). A failed training gives no gauge.

- rule = game prediction (predict_gain_act_gauge_array) on 1830/1830 training options
- alternative 'all leads +2 when any friendship' matches 1543/1830
- career 26-10-26 17-34-07-752 turn 21 (R 26-10-26 17-37-39-150) cmd 101: target 1 (act 1, bond 86, friendship), target 4 (act 4, bond 100, plain), target 5 (act 5, bond 100, plain) -> game predicts {1: 2, 4: 1, 5: 1}
- career 26-10-26 17-34-07-752 turn 21 (R 26-10-26 17-37-39-150) cmd 105: target 2 (act 2, bond 100, friendship), target 1020 (act 1, bond 0, plain), target 1128 (act 5, bond 0, plain) -> game predicts {2: 2, 1: 1, 5: 1}
- career 26-10-26 17-34-07-752 turn 21 (R 26-10-26 17-37-39-150) cmd 102: target 3 (act 3, bond 81, friendship), target 1029 (act 4, bond 0, plain) -> game predicts {3: 2, 4: 1}
- after a successful training (result_state 2) the acts' gauge moved exactly as predicted on 124/124; after a failed one (result_state != 2) no gauge moved on 1/1 (acts that levelled up excluded)

<a id="q2"></a>

## 2. 休息 / 非目标比赛 +1 加哪条 (confirmed)

Rest and outing add +1 to one act: among acts whose gauge is not full, the one with the lowest level, then the lowest gauge, then the lowest act_type. A race gives the same +1 to that act, and on some turns the game predicts more gauge for a race, sometimes for other acts as well. The source of the extra race gauge is not identified.

- rule = game prediction for rest (type 7) and outing (type 3) on 417/417 home screens
- alternative 'lowest gauge' matches 248/417
- alternative 'lowest level, full acts included' matches 88/417
- race (type 4): rule act gets >= 1 on 417/417; prediction differs from rule-only on 53/417
- 57 home screens predict nothing for rest; 57 of them are turns 1-2, summer camp (all acts training_state 2) or no act left below full and Lv10
- career 26-10-26 17-34-07-752 turn 3 (R 26-10-26 17-34-59-257) act1=Lv1/g0 act2=Lv1/g0 act3=Lv1/g0 act4=Lv1/g0 act5=Lv1/g0 -> act 1
- career 26-10-26 17-34-07-752 turn 4 (R 26-10-26 17-35-05-934) act1=Lv1/g0 act2=Lv1/g1 act3=Lv1/g0 act4=Lv1/g1 act5=Lv1/g0 -> act 1
- career 26-10-26 17-34-07-752 turn 5 (R 26-10-26 17-35-11-520) act1=Lv1/g1 act2=Lv1/g1 act3=Lv1/g0 act4=Lv1/g1 act5=Lv1/g0 -> act 3
- career 26-10-26 17-34-07-752 turn 12 (R 26-10-26 17-35-59-031) act1=Lv2/g0 act2=Lv1/g4 act3=Lv2/g2 act4=Lv1/g5 act5=Lv1/g5: rest {2: 1}, race {2: 1, 1: 3}
- career 26-10-26 17-34-07-752 turn 19 (R 26-10-26 17-37-13-774) act1=Lv3/g3 act2=Lv3/g0 act3=Lv3/g2 act4=Lv2/g5 act5=Lv3/g0: rest {2: 1}, race {2: 2, 5: 2}
- career 26-10-26 17-34-07-752 turn 31 (R 26-10-26 17-39-39-698) act1=Lv4/g5 act2=Lv4/g0 act3=Lv4/g0 act4=Lv5/g1 act5=Lv4/g0: rest {2: 1}, race {2: 2, 3: 1, 5: 1}

<a id="q3"></a>

## 3. 演目稽古是否吃卡 buff / 彩圈 (inferred)

Yes for the normal-training part. A 演目稽古 command (5101-5105) carries two parameter lists: home_info params that change with who stands in it, the same as a normal training, plus festa params (the act effect, e.g. +10 to every stat). The applied gain is their sum.

- 867 offered 演目稽古 commands in 93 (cmd, act level, act state, training level) groups; home params differ inside 86 groups, 80 of them with different partner counts
- cmd 5101 act Lv1 state 1 training Lv1: 3 partners -> home params {1: 28, 3: 4, 10: -25, 30: 16}; 3 partners -> home params {1: 38, 3: 8, 10: -25, 30: 24}; 4 partners -> home params {1: 32, 3: 5, 10: -18, 30: 21}
- cmd 5104 act Lv1 state 1 training Lv1: 2 partners -> home params {1: 4, 3: 2, 4: 25, 10: -25, 30: 15}; 3 partners -> home params {1: 4, 3: 2, 4: 26, 10: -25, 30: 16}; 4 partners -> home params {1: 8, 3: 4, 4: 37, 10: -18, 30: 29}
- applied = home params + festa params is checked per action by decode_packets.py (prediction probe, stats=match)

<a id="q4"></a>

## 4. 多演目同时亮起的交互 (confirmed)

Several acts can be full at once. Using one leaves the others full, and a full act never expires: it waits until its 演目稽古 is chosen.

- 148 turn changes started with >= 2 full acts; the acts not levelled stayed full 331 times and emptied 0 times
- act 1 stayed full over 14 turns up to career 26-10-26 18-09-09-457 turn 66 (R 26-10-26 18-22-29-944)
- career 26-10-26 17-34-07-752 turn 11 (R 26-10-26 17-35-51-912) full [1, 4, 5]; next turn act [1] levelled, [4, 5] still full
- career 26-10-26 17-34-07-752 turn 17 (R 26-10-26 17-36-56-351) full [2, 4, 5]; next turn act [2] levelled, [4, 5] still full
- career 26-10-26 17-34-07-752 turn 18 (R 26-10-26 17-37-05-301) full [4, 5]; next turn act [5] levelled, [4] still full

<a id="q5"></a>

## 5. 合宿延长量 (open)

Not observed. Every career had camp on turns 13-16, 37-40 and 61-64 (summer_camp_remain_turn_num 4 -> 1). Races entered during camp did not move these windows, but none was confirmed to be a goal race, so the extension rule is still untested.

- career 26-10-26 17-34-07-752: camp turns [(13, 16), (37, 40), (61, 64)]; races entered inside camp: none
- career 26-10-26 18-09-09-457: camp turns [(13, 16), (37, 40), (61, 64)]; races entered inside camp: [40, 64]
- career 26-10-27 12-54-07-171: camp turns [(13, 16), (37, 40), (61, 64)]; races entered inside camp: none
- career 26-10-27 13-36-46-079: camp turns [(13, 16), (37, 40), (61, 64)]; races entered inside camp: none
- career 26-10-27 14-27-06-768: camp turns [(13, 16), (37, 40), (61, 64)]; races entered inside camp: none
- career 26-10-27 18-35-17-036: camp turns [(13, 16), (37, 40), (61, 64)]; races entered inside camp: none
- 2 races entered inside camp windows in total

<a id="q6"></a>

## 6. 友人补位是否分身 (open)

A deck card can stand in two or three commands in the same turn. In normal trainings only target 1 did so in these captures. When 演目稽古 commands are on offer, every act lead (targets 1-5) shows up in several of them. The cause (card skill or scenario rule) is not identified, and the scenario friend card (target 6) never doubled.

- home screens with one target in >= 2 normal trainings, by target: {1: 59}
- home screens with one target in >= 2 commands incl. 演目稽古, by target: {4: 80, 1: 95, 2: 40, 3: 36, 5: 32}
- career 26-10-26 17-34-07-752 turn 50 (R 26-10-26 17-43-36-742) target 1 (chara 1129) in commands [101, 102]
- career 26-10-26 17-34-07-752 turn 52 (R 26-10-26 17-43-53-517) target 1 (chara 1129) in commands [101, 105]

<a id="q7"></a>

## 7. 同名双人头 (confirmed)

Deduplicated. When a deck card is the same character as an act's fixed lead (single_mode_15_act_member), no second head appears: the deck card takes over that act. All captured careers used one deck, so this rests on one character (1024).

- career 26-10-26 17-34-07-752: act_member chara 1009 (act 1) -> heads [1009]
- career 26-10-26 17-34-07-752: act_member chara 1024 (act 3) -> heads [3] = deck position 3
- career 26-10-26 17-34-07-752: act_member chara 1029 (act 4) -> heads [1029]
- career 26-10-26 17-34-07-752: act_member chara 1096 (act 2) -> heads [1096]
- career 26-10-26 17-34-07-752: act_member chara 1128 (act 5) -> heads [1128]
- career 26-10-26 18-09-09-457: act_member chara 1009 (act 1) -> heads [1009]
- career 26-10-26 18-09-09-457: act_member chara 1024 (act 3) -> heads [3] = deck position 3
- career 26-10-26 18-09-09-457: act_member chara 1029 (act 4) -> heads [1029]

<a id="q8"></a>

## 8. 教え転成是否保留基础效果 (inferred)

Partly answered. After 転成 the material advice leaves the list and the new advice records it in fusion_advice_id (e.g. 601 <- 101). fusion_effect rows are keyed by the material id, so the new advice carries its own advice_effect plus fusion_effect[material]. Whether the material's own advice_effect still applies was not measured.

- career 26-10-26 17-34-07-752: after 転成 turn 24: [(301, 0), (401, 0), (501, 0), (601, 101), (701, 201)]; turn 48: [(601, 101), (701, 201), (801, 301), (901, 401), (1001, 501)]
- career 26-10-26 18-09-09-457: after 転成 turn 24: [(301, 0), (401, 0), (501, 0), (601, 101), (701, 201)]; turn 48: [(601, 101), (701, 201), (801, 301), (901, 401), (1001, 501)]
- career 26-10-27 12-54-07-171: after 転成 turn 24: [(301, 0), (401, 0), (501, 0), (601, 101), (701, 201)]; turn 48: [(601, 101), (701, 201), (801, 301), (901, 401), (1001, 501)]

<a id="q9"></a>

## 9. 伝受 / 発表会精确回合 (confirmed)

進捗発表会 (check_point) runs at the end of turns 24, 48 and 68, after that turn's own action (training, race or event). 転成 (fusion_advice) follows the turn-24 and turn-48 発表会, then a 伝受 (attach_advice) whose response advances to turn 25 / 49. The other 伝受 are at the start of turn 3 and at the end of turns 12, 36 and 60, again advancing into the summer camp turns 13, 37 and 61. The turn-68 発表会 is followed by festa_live (本番) in the same turn. Notation below: action before->after turn.

- 1 careers: attach_advice 3->3 (after exec_command 2); attach_advice 12->13 (after race_out 12); check_point 24->24 (after get_choice_reward 24); fusion_advice 24->24 (after check_point 24); attach_advice 24->25 (after fusion_advice 24); attach_advice 36->37 (after get_choice_reward 36); check_point 48->48 (after race_out 48); fusion_advice 48->48 (after check_point 48); attach_advice 48->49 (after fusion_advice 48); attach_advice 60->61 (after get_choice_reward 60); check_point 68->68 (after exec_command 68); festa_live 68->68 (after check_point 68)
- 1 careers: attach_advice 3->3 (after exec_command 2); attach_advice 12->13 (after race_out 12); check_point 24->24 (after get_choice_reward 24); fusion_advice 24->24 (after check_point 24); attach_advice 24->25 (after fusion_advice 24); attach_advice 36->37 (after get_choice_reward 36); check_point 48->48 (after race_out 48); fusion_advice 48->48 (after check_point 48); attach_advice 48->49 (after fusion_advice 48); attach_advice 60->61 (after race_out 60); check_point 68->68 (after get_choice_reward 68); festa_live 68->68 (after check_point 68)
- 1 careers: attach_advice 3->3 (after exec_command 2); attach_advice 12->13 (after race_out 12); check_point 24->24 (after get_choice_reward 24); fusion_advice 24->24 (after check_point 24); attach_advice 24->25 (after fusion_advice 24); attach_advice 36->37 (after exec_command 36); check_point 48->48 (after race_out 48); fusion_advice 48->48 (after check_point 48); attach_advice 48->49 (after fusion_advice 48); attach_advice 60->61 (after exec_command 60); check_point 68->68 (after get_choice_reward 68); festa_live 68->68 (after check_point 68)
- 1 careers: attach_advice 3->3 (after exec_command 2); attach_advice 12->13 (after race_out 12); check_point 24->24 (after exec_command 24); fusion_advice 24->24 (after check_point 24); attach_advice 24->25 (after fusion_advice 24); attach_advice 36->37 (after exec_command 36); check_point 48->48 (after race_out 48); fusion_advice 48->48 (after check_point 48); attach_advice 48->49 (after fusion_advice 48); attach_advice 60->61 (after exec_command 60); check_point 68->68 (after get_choice_reward 68); festa_live 68->68 (after check_point 68)
- 1 careers: attach_advice 3->3 (after exec_command 2); attach_advice 12->13 (after race_out 12); check_point 24->24 (after race_out 24); fusion_advice 24->24 (after check_point 24); attach_advice 24->25 (after fusion_advice 24); attach_advice 36->37 (after exec_command 36); check_point 48->48 (after race_out 48); fusion_advice 48->48 (after check_point 48); attach_advice 48->49 (after fusion_advice 48); attach_advice 60->61 (after exec_command 60); check_point 68->68 (after exec_command 68); festa_live 68->68 (after check_point 68)
- 1 careers: attach_advice 3->3 (after exec_command 2); attach_advice 12->13 (after race_out 12); check_point 24->24 (after exec_command 24); fusion_advice 24->24 (after check_point 24); attach_advice 24->25 (after fusion_advice 24); attach_advice 36->37 (after exec_command 36); check_point 48->48 (after race_out 48); fusion_advice 48->48 (after check_point 48); attach_advice 48->49 (after fusion_advice 48); attach_advice 60->61 (after get_choice_reward 60); check_point 68->68 (after get_choice_reward 68); festa_live 68->68 (after check_point 68)

<a id="q10"></a>

## 10. finals act 效果 (inferred)

Observed with every 発表会 大成功 only. The turn-68 発表会 itself gives nothing; the event after 本番 (festa_live) gives +7 to every stat, vital +30 and skill pt +400, and the turn moves to 69. One career got +15 on guts and wiz and vital +23 instead, so part of the reward depends on state that was not isolated.

- career 26-10-26 17-34-07-752: festa_live turn 68 -> following events {'speed': 7, 'stamina': 7, 'power': 7, 'guts': 7, 'wiz': 7, 'vital': 30, 'skill_point': 400}
- career 26-10-26 18-09-09-457: festa_live turn 68 -> following events {'speed': 7, 'stamina': 7, 'power': 7, 'guts': 15, 'wiz': 15, 'vital': 23, 'skill_point': 400}
- career 26-10-27 12-54-07-171: festa_live turn 68 -> following events {'speed': 7, 'stamina': 7, 'power': 7, 'guts': 7, 'wiz': 7, 'vital': 30, 'skill_point': 400}
- career 26-10-27 13-36-46-079: festa_live turn 68 -> following events {'speed': 7, 'stamina': 7, 'power': 7, 'guts': 7, 'wiz': 7, 'vital': 30, 'skill_point': 400}
- career 26-10-27 14-27-06-768: festa_live turn 68 -> following events {'speed': 7, 'stamina': 7, 'power': 7, 'guts': 7, 'wiz': 7, 'vital': 30, 'skill_point': 400}
- career 26-10-27 18-35-17-036: festa_live turn 68 -> following events {'speed': 7, 'stamina': 7, 'power': 7, 'guts': 7, 'wiz': 7, 'vital': 30, 'skill_point': 400}

<a id="q11"></a>

## 11. 進捗発表会未达标惩罚 (open)

Not observed. Every 発表会 in the captures ended with result_type 2 and every act at or above the schedule's need_act_level, so there is no failed or partial case yet.

- (schedule_id, result_type) counts: {(1, 2): 18, (2, 2): 12, (3, 2): 6}
- min act level minus need_act_level at each 発表会: [(0, 8), (1, 4), (2, 3), (3, 3)]
- career 26-10-26 17-34-07-752: check_point turn 24 -> {'speed': 5, 'stamina': 5, 'power': 5, 'guts': 5, 'wiz': 5, 'skill_point': 200}
- career 26-10-26 17-34-07-752: check_point turn 48 -> {'speed': 10, 'stamina': 10, 'power': 10, 'guts': 10, 'wiz': 10, 'skill_point': 300}
- career 26-10-26 17-34-07-752: check_point turn 68 -> {}
- career 26-10-26 18-09-09-457: check_point turn 24 -> {'speed': 5, 'stamina': 5, 'power': 5, 'guts': 5, 'wiz': 5, 'skill_point': 200}
- career 26-10-26 18-09-09-457: check_point turn 48 -> {'speed': 10, 'stamina': 10, 'power': 10, 'guts': 10, 'wiz': 10, 'skill_point': 300}
- career 26-10-26 18-09-09-457: check_point turn 68 -> {}

<a id="q12"></a>

## 12. 组卡 ≥5 种是否含友人 (open)

Not testable from these captures: every career used the same deck.

- decks used: {(30242, 30304, 30264, 30289, 30323, 30322): 6}
