mainnet: 20230823 + workchain
testnet: 30230823 + workchainint addr = addr_hash ^ (wc + 1)// Standard actions from block.tlb:
out_list_empty$_ = OutList 0;
out_list$_ {n:#} prev:^(OutList n) action:OutAction
  = OutList (n + 1);
action_send_msg#0ec3c86d mode:(## 8) 
  out_msg:^(MessageRelaxed Any) = OutAction;
action_set_code#ad4de08e new_code:^Cell = OutAction;
action_reserve_currency#36e6b809 mode:(## 8)
  currency:CurrencyCollection = OutAction;
libref_hash$0 lib_hash:bits256 = LibRef;
libref_ref$1 library:^Cell = LibRef;
action_change_library#26fa1dd4 mode:(## 7) { mode <= 2 }
  libref:LibRef = OutAction;

// Extended actions in W5:
action_list_basic$0 {n:#} actions:^(OutList n) = ActionList n 0;
action_list_extended$1 {m:#} {n:#} action:ExtendedAction prev:^(ActionList n m) = ActionList n (m+1);

action_set_data#1ff8ea0b data:^Cell = ExtendedAction;
action_add_ext#1c40db9f addr:MsgAddressInt = ExtendedAction;
action_delete_ext#5eaef4a4 addr:MsgAddressInt = ExtendedAction;signed_request$_ 
  signature:    bits512                   // 512
  subwallet_id: uint32                    // 512+32
  valid_until:  uint32                    // 512+32+32
  msg_seqno:    uint32                    // 512+32+32+32 = 608
  inner: InnerRequest = SignedRequest;

internal_signed#7369676E signed:SignedRequest = InternalMsgBody;
internal_extension#6578746E inner:InnerRequest = InternalMsgBody;
external_signed#7369676E signed:SignedRequest = ExternalMsgBody;

actions$_ {m:#} {n:#} actions:(ActionList n m) = InnerRequest;signed_request$_ 
  signature:    bits512                   // 512
  subwallet_id: uint32                    // 512+32
  valid_until:  uint32                    // 512+32+32
  msg_seqno:    uint32                    // 512+32+32+32 = 608
  inner: InnerRequest = SignedRequest;

internal_signed#7369676E signed:SignedRequest = InternalMsgBody;
internal_extension#6578746E inner:InnerRequest = InternalMsgBody;
external_signed#7369676E signed:SignedRequest = ExternalMsgBody;

actions$_ {m:#} {n:#} actions:(ActionList n m) = InnerRequest;## Contest note!

<div align="center">
<img alt="Contest logo" src="contest.png" height="280" width="280">
</div>

**Because of the extreme amount of optimizations, developer's discretion is advised!** *Evil laugh*

The build system is the same as in the original Wallet V5, **no security features have been sacrificed**
for performance improvements, that is - there are **practically no tradeoffs or compromises**.

Message and storage layouts were **not changed**, although some rearragement might squeeze a little more gas,
but that may break existing optimizations due to stack reordering.

Also, **tests were improved** - a **Global Gas Counter** mechanism was added that accounts for gas in all transactions
of all test suites and cases (except for negative and getter ones). This allows to keep an eye on other non-contest
cases to track how bad is tradeoff when performing optimizations here and there.

Another utility that was developed for contest is ***scalpel script***, that allows for a detailed, *really* detailed optimizations
of the code by comparing lines of code function by function, printing out diffs, and providing detailed TVM files with
stack comments and rewrites. This utility allowed to make some latter optimizations, since with each optimization
next one becomes exponentionally harder to make. While result is not entirely precise and is needed to be verified
by tests, this allows to instantly estimate whether there is some progress or not, since scalpel is executed immediately,
while tests take approximately 10 seconds to execute.

### Details of optimizations, their rationale and explanations, comparison of consumed gas both in test cases and not in test cases (global gas counter) are provided on a dedicated page: [Gas improvements](Improvements.rst).
