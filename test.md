All real ports: 12596 14325 15012 17208 19811

All proxy ports: 17625 14517 13014 14631 19147

node 2 Proxy started

node 4 Proxy started

node 3 Proxy started

node 0 Proxy started

node 1 Proxy started

2024/04/11 20:42:12 Start listening to port: 19811

2024/04/11 20:42:12 Start listening to port: 14325

2024/04/11 20:42:12 Start listening to port: 15012

2024/04/11 20:42:12 Start listening to port: 12596

2024/04/11 20:42:12 Start listening to port: 17208

2024/04/11 20:42:12 Successfully connect all nodes

2024/04/11 20:42:12 Successfully connect all nodes

2024/04/11 20:42:12 Successfully connect all nodes

2024/04/11 20:42:12 Successfully connect all nodes

2024/04/11 20:42:12 Successfully connect all nodes

Running testOneCandidateOneRoundElection:

node 1 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 2 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 4 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 3 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 3: dropped RequestVoteResponse to 0

node 2 -> 0: granted, term: 1

node 1 -> 0: granted, term: 1

node 4: dropped RequestVoteResponse to 0

node 4 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 1 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 2 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 3 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 1 -> 0: success, term: 1, matchIdx: 0

node 2 -> 0: success, term: 1, matchIdx: 0

node 4 -> 0: success, term: 1, matchIdx: 0

node 3 -> 0: success, term: 1, matchIdx: 0

testOneCandidateOneRoundElection PASS

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 118: 49262 Killed: 9        ${RAFT_NODE} ${NODE_PORT3} ${ALL_PROXY_PORTS} 3 10000 10000

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 119: 49257 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT3} -proxyport=${PROXY_NODE_PORT3} -id=3

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 120: 49255 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT1} -proxyport=${PROXY_NODE_PORT1} -id=1

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 121: 49258 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT4} -proxyport=${PROXY_NODE_PORT4} -id=4

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 122: 49254 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT0} -proxyport=${PROXY_NODE_PORT0} -id=0

node 1 Proxy started

node 2 Proxy started

node 0 Proxy started

node 4 Proxy started

node 3 Proxy started

2024/04/11 20:42:15 Start listening to port: 12596

2024/04/11 20:42:15 Start listening to port: 14325

2024/04/11 20:42:15 Successfully connect all nodes

2024/04/11 20:42:15 Successfully connect all nodes

2024/04/11 20:42:15 Start listening to port: 15012

2024/04/11 20:42:15 Start listening to port: 17208

2024/04/11 20:42:15 Start listening to port: 19811

2024/04/11 20:42:15 Successfully connect all nodes

2024/04/11 20:42:15 Successfully connect all nodes

2024/04/11 20:42:15 Successfully connect all nodes

Running testOneCandidateStartTwoElection:

node 3: dropped RequestVote from 0

node 4: dropped RequestVote from 0

node 2: dropped RequestVote from 0

node 1 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 1 -> 0: granted, term: 1

node 1 <- 0: RequestVote -- term: 2, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 4 <- 0: RequestVote -- term: 2, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 3 <- 0: RequestVote -- term: 2, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 2 <- 0: RequestVote -- term: 2, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 1: dropped RequestVoteResponse to 0

node 2 -> 0: granted, term: 2

node 4 -> 0: granted, term: 2

node 3 -> 0: granted, term: 2

node 2 <- 0: AppendEntries -- term: 2, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 1 <- 0: AppendEntries -- term: 2, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 4 <- 0: AppendEntries -- term: 2, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 3 <- 0: AppendEntries -- term: 2, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 2 -> 0: success, term: 2, matchIdx: 0

node 1 -> 0: success, term: 2, matchIdx: 0

node 3 -> 0: success, term: 2, matchIdx: 0

node 4 -> 0: success, term: 2, matchIdx: 0

testOneCandidateStartTwoElection PASS

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 118: 49272 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT1} -proxyport=${PROXY_NODE_PORT1} -id=1

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 120: 49271 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT0} -proxyport=${PROXY_NODE_PORT0} -id=0

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 126: 49275 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT4} -proxyport=${PROXY_NODE_PORT4} -id=4

node 3 Proxy started

node 2 Proxy started

node 0 Proxy started

node 1 Proxy started

2024/04/11 20:42:19 Start listening to port: 14325

2024/04/11 20:42:19 Start listening to port: 17208

node 4 Proxy started

2024/04/11 20:42:19 Start listening to port: 12596

2024/04/11 20:42:19 Start listening to port: 19811

2024/04/11 20:42:19 Successfully connect all nodes

2024/04/11 20:42:19 Successfully connect all nodes

2024/04/11 20:42:19 Successfully connect all nodes

2024/04/11 20:42:19 Start listening to port: 15012

2024/04/11 20:42:19 Successfully connect all nodes

2024/04/11 20:42:19 Successfully connect all nodes

Running testTwoCandidateForElection:

node 4: dropped RequestVote from 0

node 2: dropped RequestVote from 0

node 3: dropped RequestVote from 0

node 1 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 1 -> 0: granted, term: 1

node 3 <- 2: RequestVote -- term: 1, candidateId: 2, lastLogIdx: 0, lastLogTerm: 0

node 4 <- 2: RequestVote -- term: 1, candidateId: 2, lastLogIdx: 0, lastLogTerm: 0

node 1 <- 2: RequestVote -- term: 1, candidateId: 2, lastLogIdx: 0, lastLogTerm: 0

node 0 <- 2: RequestVote -- term: 1, candidateId: 2, lastLogIdx: 0, lastLogTerm: 0

node 1 -> 2: reject, term: 1

node 4 -> 2: granted, term: 1

node 3 -> 2: granted, term: 1

node 0 -> 2: reject, term: 1

node 0 <- 2: AppendEntries -- term: 1, leaderId: 2, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 3 <- 2: AppendEntries -- term: 1, leaderId: 2, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 4 <- 2: AppendEntries -- term: 1, leaderId: 2, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 1 <- 2: AppendEntries -- term: 1, leaderId: 2, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 0 -> 2: success, term: 1, matchIdx: 0

node 4 -> 2: success, term: 1, matchIdx: 0

node 3 -> 2: success, term: 1, matchIdx: 0

node 1 -> 2: success, term: 1, matchIdx: 0

testTwoCandidateForElection PASS

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 114: 49291 Killed: 9        ${RAFT_NODE} ${NODE_PORT0} ${ALL_PROXY_PORTS} 0 10000 10000

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 114: 49293 Killed: 9        ${RAFT_NODE} ${NODE_PORT2} ${ALL_PROXY_PORTS} 2 10000 10000

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 114: 49294 Killed: 9        ${RAFT_NODE} ${NODE_PORT3} ${ALL_PROXY_PORTS} 3 10000 10000

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 115: 49292 Killed: 9        ${RAFT_NODE} ${NODE_PORT1} ${ALL_PROXY_PORTS} 1 10000 10000

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 119: 49286 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT0} -proxyport=${PROXY_NODE_PORT0} -id=0

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 119: 49288 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT2} -proxyport=${PROXY_NODE_PORT2} -id=2

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 120: 49287 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT1} -proxyport=${PROXY_NODE_PORT1} -id=1

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 120: 49295 Killed: 9        ${RAFT_NODE} ${NODE_PORT4} ${ALL_PROXY_PORTS} 4 10000 10000

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 125: 49289 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT3} -proxyport=${PROXY_NODE_PORT3} -id=3

node 1 Proxy started

node 0 Proxy started

node 2 Proxy started

node 4 Proxy started

2024/04/11 20:42:24 Start listening to port: 12596

2024/04/11 20:42:24 Start listening to port: 14325

node 3 Proxy started

2024/04/11 20:42:24 Start listening to port: 17208

2024/04/11 20:42:24 Start listening to port: 15012

2024/04/11 20:42:24 Successfully connect all nodes

2024/04/11 20:42:24 Successfully connect all nodes

2024/04/11 20:42:24 Successfully connect all nodes

2024/04/11 20:42:24 Successfully connect all nodes

2024/04/11 20:42:24 Start listening to port: 19811

2024/04/11 20:42:24 Successfully connect all nodes

Running testSplitVote:

node 4 <- 3: RequestVote -- term: 1, candidateId: 3, lastLogIdx: 0, lastLogTerm: 0

node 1 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 2 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 2: dropped RequestVoteResponse to 0

node 1 -> 0: granted, term: 1

node 4 -> 3: granted, term: 1

node 1 <- 3: RequestVote -- term: 2, candidateId: 3, lastLogIdx: 0, lastLogTerm: 0

node 0 <- 3: RequestVote -- term: 2, candidateId: 3, lastLogIdx: 0, lastLogTerm: 0

node 4 <- 3: RequestVote -- term: 2, candidateId: 3, lastLogIdx: 0, lastLogTerm: 0

node 2 <- 3: RequestVote -- term: 2, candidateId: 3, lastLogIdx: 0, lastLogTerm: 0

node 0 -> 3: granted, term: 2

node 1 -> 3: granted, term: 2

node 2 -> 3: granted, term: 2

node 4 -> 3: granted, term: 2

node 4 <- 3: AppendEntries -- term: 2, leaderId: 3, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 2 <- 3: AppendEntries -- term: 2, leaderId: 3, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 1 <- 3: AppendEntries -- term: 2, leaderId: 3, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 0 <- 3: AppendEntries -- term: 2, leaderId: 3, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 2 -> 3: success, term: 2, matchIdx: 0

node 4 -> 3: success, term: 2, matchIdx: 0

node 1 -> 3: success, term: 2, matchIdx: 0

node 0 -> 3: success, term: 2, matchIdx: 0

node 0 <- 3: RequestVote -- term: 1, candidateId: 3, lastLogIdx: 0, lastLogTerm: 0

node 3 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 2 <- 3: RequestVote -- term: 1, candidateId: 3, lastLogIdx: 0, lastLogTerm: 0

node 1 <- 3: RequestVote -- term: 1, candidateId: 3, lastLogIdx: 0, lastLogTerm: 0

node 4 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 1 -> 3: reject, term: 2

node 2 -> 3: reject, term: 2

node 3 -> 0: reject, term: 2

node 0 -> 3: reject, term: 2

node 4 -> 0: reject, term: 2

testSplitVote PASS

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 112: 49306 Killed: 9        ${RAFT_NODE} ${NODE_PORT0} ${ALL_PROXY_PORTS} 0 10000 10000

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 112: 49308 Killed: 9        ${RAFT_NODE} ${NODE_PORT2} ${ALL_PROXY_PORTS} 2 10000 10000

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 114: 49307 Killed: 9        ${RAFT_NODE} ${NODE_PORT1} ${ALL_PROXY_PORTS} 1 10000 10000

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 114: 49309 Killed: 9        ${RAFT_NODE} ${NODE_PORT3} ${ALL_PROXY_PORTS} 3 10000 10000

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 114: 49310 Killed: 9        ${RAFT_NODE} ${NODE_PORT4} ${ALL_PROXY_PORTS} 4 10000 10000

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 115: 49303 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT2} -proxyport=${PROXY_NODE_PORT2} -id=2

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 120: 49301 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT0} -proxyport=${PROXY_NODE_PORT0} -id=0

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 120: 49302 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT1} -proxyport=${PROXY_NODE_PORT1} -id=1

node 1 Proxy started

node 2 Proxy started

node 0 Proxy started

node 3 Proxy started

node 4 Proxy started

2024/04/11 20:42:29 Start listening to port: 12596

2024/04/11 20:42:29 Start listening to port: 15012

2024/04/11 20:42:29 Start listening to port: 14325

2024/04/11 20:42:29 Successfully connect all nodes

2024/04/11 20:42:29 Start listening to port: 17208

2024/04/11 20:42:29 Start listening to port: 19811

2024/04/11 20:42:29 Successfully connect all nodes

2024/04/11 20:42:29 Successfully connect all nodes

2024/04/11 20:42:29 Successfully connect all nodes

2024/04/11 20:42:29 Successfully connect all nodes

Running testAllForElection:

node 4 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 4 <- 3: RequestVote -- term: 1, candidateId: 3, lastLogIdx: 0, lastLogTerm: 0

node 3 <- 1: RequestVote -- term: 1, candidateId: 1, lastLogIdx: 0, lastLogTerm: 0

node 3 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 1 <- 3: RequestVote -- term: 1, candidateId: 3, lastLogIdx: 0, lastLogTerm: 0

node 1 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 4 <- 1: RequestVote -- term: 1, candidateId: 1, lastLogIdx: 0, lastLogTerm: 0

node 0 <- 3: RequestVote -- term: 1, candidateId: 3, lastLogIdx: 0, lastLogTerm: 0

node 3 -> 1: reject, term: 1

node 0 <- 1: RequestVote -- term: 1, candidateId: 1, lastLogIdx: 0, lastLogTerm: 0

node 3 -> 0: reject, term: 1

node 4 -> 1: reject, term: 1

node 4 -> 0: reject, term: 1

node 4 -> 3: reject, term: 1

node 0 <- 4: RequestVote -- term: 1, candidateId: 4, lastLogIdx: 0, lastLogTerm: 0

node 0 <- 2: RequestVote -- term: 1, candidateId: 2, lastLogIdx: 0, lastLogTerm: 0

node 3 <- 2: RequestVote -- term: 1, candidateId: 2, lastLogIdx: 0, lastLogTerm: 0

node 3 <- 4: RequestVote -- term: 1, candidateId: 4, lastLogIdx: 0, lastLogTerm: 0

node 2 <- 4: RequestVote -- term: 1, candidateId: 4, lastLogIdx: 0, lastLogTerm: 0

node 2 <- 1: RequestVote -- term: 1, candidateId: 1, lastLogIdx: 0, lastLogTerm: 0

node 2 <- 3: RequestVote -- term: 1, candidateId: 3, lastLogIdx: 0, lastLogTerm: 0

node 2 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 1 -> 3: reject, term: 1

node 4 <- 2: RequestVote -- term: 1, candidateId: 2, lastLogIdx: 0, lastLogTerm: 0

node 1 -> 0: reject, term: 1

node 3 -> 4: reject, term: 1

node 3 -> 2: reject, term: 1

node 1 <- 2: RequestVote -- term: 1, candidateId: 2, lastLogIdx: 0, lastLogTerm: 0

node 4 -> 2: reject, term: 1

node 1 <- 4: RequestVote -- term: 1, candidateId: 4, lastLogIdx: 0, lastLogTerm: 0

node 1 -> 2: reject, term: 1

node 1 -> 4: reject, term: 1

node 0 -> 4: reject, term: 1

node 0 -> 2: reject, term: 1

node 0 -> 3: reject, term: 1

node 0 -> 1: reject, term: 1

node 2 -> 3: reject, term: 1

node 2 -> 0: reject, term: 1

node 2 -> 4: reject, term: 1

node 2 -> 1: reject, term: 1

node 1 <- 4: RequestVote -- term: 2, candidateId: 4, lastLogIdx: 0, lastLogTerm: 0

node 3 <- 4: RequestVote -- term: 2, candidateId: 4, lastLogIdx: 0, lastLogTerm: 0

node 0 <- 4: RequestVote -- term: 2, candidateId: 4, lastLogIdx: 0, lastLogTerm: 0

node 2 <- 4: RequestVote -- term: 2, candidateId: 4, lastLogIdx: 0, lastLogTerm: 0

node 3 -> 4: granted, term: 2

node 1 -> 4: granted, term: 2

node 2 -> 4: granted, term: 2

node 0 -> 4: granted, term: 2

node 1 <- 4: AppendEntries -- term: 2, leaderId: 4, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 0 <- 4: AppendEntries -- term: 2, leaderId: 4, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 3 <- 4: AppendEntries -- term: 2, leaderId: 4, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 2 <- 4: AppendEntries -- term: 2, leaderId: 4, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 1 -> 4: success, term: 2, matchIdx: 0

node 3 -> 4: success, term: 2, matchIdx: 0

node 0 -> 4: success, term: 2, matchIdx: 0

node 2 -> 4: success, term: 2, matchIdx: 0

testAllForElection PASS

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 107: 49321 Killed: 9        ${RAFT_NODE} ${NODE_PORT0} ${ALL_PROXY_PORTS} 0 10000 10000

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 107: 49322 Killed: 9        ${RAFT_NODE} ${NODE_PORT1} ${ALL_PROXY_PORTS} 1 10000 10000

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 108: 49323 Killed: 9        ${RAFT_NODE} ${NODE_PORT2} ${ALL_PROXY_PORTS} 2 10000 10000

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 113: 49324 Killed: 9        ${RAFT_NODE} ${NODE_PORT3} ${ALL_PROXY_PORTS} 3 10000 10000

node 1 Proxy started

node 3 Proxy started

node 4 Proxy started

2024/04/11 20:42:33 Start listening to port: 12596

node 0 Proxy started

node 2 Proxy started

2024/04/11 20:42:33 Start listening to port: 14325

2024/04/11 20:42:33 Start listening to port: 17208

2024/04/11 20:42:33 Start listening to port: 19811

2024/04/11 20:42:33 Successfully connect all nodes

2024/04/11 20:42:33 Start listening to port: 15012

2024/04/11 20:42:33 Successfully connect all nodes

2024/04/11 20:42:33 Successfully connect all nodes

2024/04/11 20:42:33 Successfully connect all nodes

2024/04/11 20:42:33 Successfully connect all nodes

Running testLeaderRevertToFollower:

node 4 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 2 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 1 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 3 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 4 -> 0: granted, term: 1

node 2 -> 0: granted, term: 1

node 1 -> 0: granted, term: 1

node 3 -> 0: granted, term: 1

node 4 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 3 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 1 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 2 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 3 -> 0: success, term: 1, matchIdx: 0

node 1 -> 0: success, term: 1, matchIdx: 0

node 2 -> 0: success, term: 1, matchIdx: 0

node 4 -> 0: success, term: 1, matchIdx: 0

node 0: dropped RequestVote from 4

node 2: dropped RequestVote from 4

node 3 <- 4: RequestVote -- term: 2, candidateId: 4, lastLogIdx: 0, lastLogTerm: 0

node 1 <- 4: RequestVote -- term: 2, candidateId: 4, lastLogIdx: 0, lastLogTerm: 0

node 1 -> 4: granted, term: 2

node 3 -> 4: granted, term: 2

node 3 <- 4: AppendEntries -- term: 2, leaderId: 4, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 1 <- 4: AppendEntries -- term: 2, leaderId: 4, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 2 <- 4: AppendEntries -- term: 2, leaderId: 4, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 3 -> 4: success, term: 2, matchIdx: 0

node 1 -> 4: success, term: 2, matchIdx: 0

node 0 <- 4: AppendEntries -- term: 2, leaderId: 4, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 2 -> 4: success, term: 2, matchIdx: 0

node 0 -> 4: success, term: 2, matchIdx: 0

testLeaderRevertToFollower PASS

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 111: 49354 Killed: 9        ${RAFT_NODE} ${NODE_PORT0} ${ALL_PROXY_PORTS} 0 10000 10000

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 111: 49355 Killed: 9        ${RAFT_NODE} ${NODE_PORT1} ${ALL_PROXY_PORTS} 1 10000 10000

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 112: 49356 Killed: 9        ${RAFT_NODE} ${NODE_PORT2} ${ALL_PROXY_PORTS} 2 10000 10000

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 114: 49350 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT1} -proxyport=${PROXY_NODE_PORT1} -id=1

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 114: 49351 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT2} -proxyport=${PROXY_NODE_PORT2} -id=2

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 114: 49357 Killed: 9        ${RAFT_NODE} ${NODE_PORT3} ${ALL_PROXY_PORTS} 3 10000 10000

node 2 Proxy started

node 1 Proxy started

node 0 Proxy started

2024/04/11 20:42:37 Start listening to port: 12596

node 3 Proxy started

node 4 Proxy started

2024/04/11 20:42:37 Start listening to port: 14325

2024/04/11 20:42:37 Start listening to port: 17208

2024/04/11 20:42:37 Start listening to port: 15012

2024/04/11 20:42:37 Successfully connect all nodes

2024/04/11 20:42:37 Start listening to port: 19811

2024/04/11 20:42:37 Successfully connect all nodes

2024/04/11 20:42:37 Successfully connect all nodes

2024/04/11 20:42:37 Successfully connect all nodes

2024/04/11 20:42:37 Successfully connect all nodes

Running testOneSimplePut:

node 2 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 4 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 3 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 4 -> 0: granted, term: 1

node 2 -> 0: granted, term: 1

node 2 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 4 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 3 -> 0: granted, term: 1

node 1 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 3 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 1 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 4 -> 0: success, term: 1, matchIdx: 0

node 2 -> 0: success, term: 1, matchIdx: 0

node 1 -> 0: reject, term: 1

node 1 -> 0: success, term: 1, matchIdx: 0

node 3 -> 0: success, term: 1, matchIdx: 0

2024/04/11 20:42:40 Receive propose from client

2024/04/11 20:42:40 Receive propose from client

2024/04/11 20:42:40 Receive propose from client

2024/04/11 20:42:40 Receive propose from client

2024/04/11 20:42:40 Receive propose from client

node 1 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [t1:put<test,1528259135>], leaderCommit: 0

node 2 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [t1:put<test,1528259135>], leaderCommit: 0

node 3 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [t1:put<test,1528259135>], leaderCommit: 0

node 4 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [t1:put<test,1528259135>], leaderCommit: 0

node 2 -> 0: success, term: 1, matchIdx: 1

node 1 -> 0: success, term: 1, matchIdx: 1

node 4 -> 0: success, term: 1, matchIdx: 1

node 3 -> 0: success, term: 1, matchIdx: 1

node 3 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 1, prevLogTerm: 1, entries: [], leaderCommit: 1

node 2 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 1, prevLogTerm: 1, entries: [], leaderCommit: 1

node 1 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 1, prevLogTerm: 1, entries: [], leaderCommit: 1

node 4 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 1, prevLogTerm: 1, entries: [], leaderCommit: 1

node 2 -> 0: success, term: 1, matchIdx: 1

node 4 -> 0: success, term: 1, matchIdx: 1

node 3 -> 0: success, term: 1, matchIdx: 1

node 1 -> 0: success, term: 1, matchIdx: 1

20:42:42.066894 rafttest.go:262: FAIL: error on testOneSimplePut - 

Expected:

node 1 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 1 -> 0: granted, term: 1

node 1 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 1 -> 0: success, term: 1, matchIdx: 0

node 1 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [t1:put<test,1528259135>], leaderCommit: 0

node 1 -> 0: success, term: 1, matchIdx: 1

node 1 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 1, prevLogTerm: 1, entries: [], leaderCommit: 1

node 1 -> 0: success, term: 1, matchIdx: 1

But get:

node 1 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 1 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 1 -> 0: reject, term: 1

node 1 -> 0: success, term: 1, matchIdx: 0

node 1 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [t1:put<test,1528259135>], leaderCommit: 0

node 1 -> 0: success, term: 1, matchIdx: 1

node 1 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 1, prevLogTerm: 1, entries: [], leaderCommit: 1

node 1 -> 0: success, term: 1, matchIdx: 1

testOneSimplePut FAIL

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 114: 49371 Killed: 9        ${RAFT_NODE} ${NODE_PORT2} ${ALL_PROXY_PORTS} 2 10000 10000

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 115: 49364 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT0} -proxyport=${PROXY_NODE_PORT0} -id=0

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 115: 49369 Killed: 9        ${RAFT_NODE} ${NODE_PORT0} ${ALL_PROXY_PORTS} 0 10000 10000

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 115: 49370 Killed: 9        ${RAFT_NODE} ${NODE_PORT1} ${ALL_PROXY_PORTS} 1 10000 10000

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 115: 49372 Killed: 9        ${RAFT_NODE} ${NODE_PORT3} ${ALL_PROXY_PORTS} 3 10000 10000

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 115: 49373 Killed: 9        ${RAFT_NODE} ${NODE_PORT4} ${ALL_PROXY_PORTS} 4 10000 10000

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 121: 49365 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT1} -proxyport=${PROXY_NODE_PORT1} -id=1

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 121: 49366 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT2} -proxyport=${PROXY_NODE_PORT2} -id=2

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 121: 49367 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT3} -proxyport=${PROXY_NODE_PORT3} -id=3

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 128: 49368 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT4} -proxyport=${PROXY_NODE_PORT4} -id=4

node 2 Proxy started

node 3 Proxy started

node 0 Proxy started

2024/04/11 20:42:42 Start listening to port: 12596

node 1 Proxy started

2024/04/11 20:42:42 Start listening to port: 14325

node 4 Proxy started

2024/04/11 20:42:42 Start listening to port: 17208

2024/04/11 20:42:42 Successfully connect all nodes

2024/04/11 20:42:42 Successfully connect all nodes

2024/04/11 20:42:42 Successfully connect all nodes

2024/04/11 20:42:42 Start listening to port: 15012

2024/04/11 20:42:42 Start listening to port: 19811

2024/04/11 20:42:42 Successfully connect all nodes

2024/04/11 20:42:42 Successfully connect all nodes

Running testOneSimpleUpdate:

node 2 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 3 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 1 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 4 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 1 -> 0: granted, term: 1

node 2 -> 0: granted, term: 1

node 3 -> 0: granted, term: 1

node 4 -> 0: granted, term: 1

node 3 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 4 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 2 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 1 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 4 -> 0: success, term: 1, matchIdx: 0

node 3 -> 0: success, term: 1, matchIdx: 0

node 1 -> 0: success, term: 1, matchIdx: 0

node 2 -> 0: success, term: 1, matchIdx: 0

2024/04/11 20:42:45 Receive propose from client

node 4: dropped AppendEntries from 0

node 3: dropped AppendEntries from 0

node 2 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [t1:put<test,842267538>], leaderCommit: 0

node 1 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [t1:put<test,842267538>], leaderCommit: 0

node 2 -> 0: success, term: 1, matchIdx: 1

node 1 -> 0: success, term: 1, matchIdx: 1

2024/04/11 20:42:46 Receive propose from client

node 1 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 1, prevLogTerm: 1, entries: [t1:put<test,2080568283>], leaderCommit: 1

node 4 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [t1:put<test,842267538>,t1:put<test,2080568283>], leaderCommit: 1

node 2 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 1, prevLogTerm: 1, entries: [t1:put<test,2080568283>], leaderCommit: 1

node 3 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [t1:put<test,842267538>,t1:put<test,2080568283>], leaderCommit: 1

node 4 -> 0: success, term: 1, matchIdx: 2

node 1 -> 0: success, term: 1, matchIdx: 2

node 2 -> 0: success, term: 1, matchIdx: 2

node 3 -> 0: success, term: 1, matchIdx: 2

node 1 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 2, prevLogTerm: 1, entries: [], leaderCommit: 2

node 3 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 2, prevLogTerm: 1, entries: [], leaderCommit: 2

node 4 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 2, prevLogTerm: 1, entries: [], leaderCommit: 2

node 2 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 2, prevLogTerm: 1, entries: [], leaderCommit: 2

node 3 -> 0: success, term: 1, matchIdx: 2

node 1 -> 0: success, term: 1, matchIdx: 2

node 4 -> 0: success, term: 1, matchIdx: 2

node 2 -> 0: success, term: 1, matchIdx: 2

testOneSimpleUpdate PASS

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 121: 49382 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT2} -proxyport=${PROXY_NODE_PORT2} -id=2

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 122: 49384 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT4} -proxyport=${PROXY_NODE_PORT4} -id=4

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 127: 49383 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT3} -proxyport=${PROXY_NODE_PORT3} -id=3

node 0 Proxy started

node 1 Proxy started

node 2 Proxy started

node 4 Proxy started

node 3 Proxy started

2024/04/11 20:42:48 Start listening to port: 12596

2024/04/11 20:42:48 Start listening to port: 17208

2024/04/11 20:42:48 Start listening to port: 15012

2024/04/11 20:42:48 Start listening to port: 14325

2024/04/11 20:42:48 Successfully connect all nodes

2024/04/11 20:42:48 Successfully connect all nodes

2024/04/11 20:42:48 Successfully connect all nodes

2024/04/11 20:42:48 Successfully connect all nodes

2024/04/11 20:42:48 Start listening to port: 19811

2024/04/11 20:42:48 Successfully connect all nodes

Running testOneSimpleDelete:

node 2 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 3 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 4 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 1 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 3 -> 0: granted, term: 1

node 4 -> 0: granted, term: 1

node 2 -> 0: granted, term: 1

node 2 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 4 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 1 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 1 -> 0: granted, term: 1

node 3 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 4 -> 0: success, term: 1, matchIdx: 0

node 3 -> 0: success, term: 1, matchIdx: 0

node 2 -> 0: success, term: 1, matchIdx: 0

node 1 -> 0: success, term: 1, matchIdx: 0

2024/04/11 20:42:51 Receive propose from client

node 4: dropped AppendEntries from 0

node 1: dropped AppendEntries from 0

node 3: dropped AppendEntries from 0

node 2 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [t1:put<test,276146172>], leaderCommit: 0

node 2 -> 0: success, term: 1, matchIdx: 1

2024/04/11 20:42:52 Receive propose from client

node 2: dropped AppendEntries from 0

node 4: dropped AppendEntries from 0

node 1: dropped AppendEntries from 0

node 3 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [t1:put<test,276146172>,t1:del<test,0>], leaderCommit: 0

node 3 -> 0: success, term: 1, matchIdx: 2

node 3 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 2, prevLogTerm: 1, entries: [], leaderCommit: 1

node 1 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [t1:put<test,276146172>,t1:del<test,0>], leaderCommit: 1

node 2 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 1, prevLogTerm: 1, entries: [t1:del<test,0>], leaderCommit: 1

node 4 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [t1:put<test,276146172>,t1:del<test,0>], leaderCommit: 1

node 3 -> 0: success, term: 1, matchIdx: 2

node 1 -> 0: success, term: 1, matchIdx: 2

node 2 -> 0: success, term: 1, matchIdx: 2

node 4 -> 0: success, term: 1, matchIdx: 2

node 4 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 2, prevLogTerm: 1, entries: [], leaderCommit: 2

node 2 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 2, prevLogTerm: 1, entries: [], leaderCommit: 2

node 3 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 2, prevLogTerm: 1, entries: [], leaderCommit: 2

node 1 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 2, prevLogTerm: 1, entries: [], leaderCommit: 2

node 3 -> 0: success, term: 1, matchIdx: 2

node 4 -> 0: success, term: 1, matchIdx: 2

node 2 -> 0: success, term: 1, matchIdx: 2

node 1 -> 0: success, term: 1, matchIdx: 2

testOneSimpleDelete PASS

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 114: 49413 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT0} -proxyport=${PROXY_NODE_PORT0} -id=0

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 114: 49418 Killed: 9        ${RAFT_NODE} ${NODE_PORT0} ${ALL_PROXY_PORTS} 0 10000 10000

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 114: 49419 Killed: 9        ${RAFT_NODE} ${NODE_PORT1} ${ALL_PROXY_PORTS} 1 10000 10000

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 114: 49422 Killed: 9        ${RAFT_NODE} ${NODE_PORT4} ${ALL_PROXY_PORTS} 4 10000 10000

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 115: 49421 Killed: 9        ${RAFT_NODE} ${NODE_PORT3} ${ALL_PROXY_PORTS} 3 10000 10000

node 0 Proxy started

node 3 Proxy started

node 1 Proxy started

node 4 Proxy started

2024/04/11 20:42:55 Start listening to port: 12596

node 2 Proxy started

2024/04/11 20:42:55 Start listening to port: 14325

2024/04/11 20:42:55 Start listening to port: 15012

2024/04/11 20:42:55 Successfully connect all nodes

2024/04/11 20:42:55 Successfully connect all nodes

2024/04/11 20:42:55 Successfully connect all nodes

2024/04/11 20:42:55 Start listening to port: 17208

2024/04/11 20:42:55 Start listening to port: 19811

2024/04/11 20:42:55 Successfully connect all nodes

2024/04/11 20:42:55 Successfully connect all nodes

Running testDeleteNonExistKey:

node 4 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 3 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 2 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 1 <- 0: RequestVote -- term: 1, candidateId: 0, lastLogIdx: 0, lastLogTerm: 0

node 4 -> 0: granted, term: 1

node 3 -> 0: granted, term: 1

node 2 -> 0: granted, term: 1

node 1 -> 0: granted, term: 1

node 1 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 3 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 2 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 4 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [], leaderCommit: 0

node 1 -> 0: success, term: 1, matchIdx: 0

node 3 -> 0: success, term: 1, matchIdx: 0

node 4 -> 0: success, term: 1, matchIdx: 0

node 2 -> 0: success, term: 1, matchIdx: 0

2024/04/11 20:42:58 Receive propose from client

node 2: dropped AppendEntries from 0

node 3: dropped AppendEntries from 0

node 4 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [t1:put<test,2102755969>], leaderCommit: 0

node 1 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [t1:put<test,2102755969>], leaderCommit: 0

node 4: dropped AppendEntriesResponse to 0

node 1: dropped AppendEntriesResponse to 0

2024/04/11 20:42:59 Receive propose from client

node 4: dropped AppendEntries from 0

node 1 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [t1:put<test,2102755969>,t1:del<test2,0>], leaderCommit: 0

node 3 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [t1:put<test,2102755969>,t1:del<test2,0>], leaderCommit: 0

node 2: dropped AppendEntries from 0

node 1: dropped AppendEntriesResponse to 0

node 3: dropped AppendEntriesResponse to 0

node 4 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [t1:put<test,2102755969>,t1:del<test2,0>], leaderCommit: 0

node 3 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [t1:put<test,2102755969>,t1:del<test2,0>], leaderCommit: 0

node 1 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [t1:put<test,2102755969>,t1:del<test2,0>], leaderCommit: 0

node 2 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 0, prevLogTerm: 0, entries: [t1:put<test,2102755969>,t1:del<test2,0>], leaderCommit: 0

node 4 -> 0: success, term: 1, matchIdx: 2

node 3 -> 0: success, term: 1, matchIdx: 2

node 1 -> 0: success, term: 1, matchIdx: 2

node 2 -> 0: success, term: 1, matchIdx: 2

node 2 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 2, prevLogTerm: 1, entries: [], leaderCommit: 2

node 3 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 2, prevLogTerm: 1, entries: [], leaderCommit: 2

node 4 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 2, prevLogTerm: 1, entries: [], leaderCommit: 2

node 1 <- 0: AppendEntries -- term: 1, leaderId: 0, prevLogIdx: 2, prevLogTerm: 1, entries: [], leaderCommit: 2

node 4 -> 0: success, term: 1, matchIdx: 2

node 2 -> 0: success, term: 1, matchIdx: 2

node 3 -> 0: success, term: 1, matchIdx: 2

node 1 -> 0: success, term: 1, matchIdx: 2

testDeleteNonExistKey PASS

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 112: 49436 Killed: 9        ${RAFT_NODE} ${NODE_PORT2} ${ALL_PROXY_PORTS} 2 10000 10000

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 118: 49431 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT2} -proxyport=${PROXY_NODE_PORT2} -id=2

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 118: 49437 Killed: 9        ${RAFT_NODE} ${NODE_PORT3} ${ALL_PROXY_PORTS} 3 10000 10000

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 119: 49433 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT4} -proxyport=${PROXY_NODE_PORT4} -id=4

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 122: 49430 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT1} -proxyport=${PROXY_NODE_PORT1} -id=1

/Users/zj2048/CSlearning/cuhk/cuhk-raft-Chord2048/scripts/rafttest_single.sh: line 122: 49432 Killed: 9        ${PROXY_NODE} -raftport=${NODE_PORT3} -proxyport=${PROXY_NODE_PORT3} -id=3

2024/04/11 20:42:15 testOneCandidateOneRoundElection PASS

2024/04/11 20:42:19 testOneCandidateStartTwoElection PASS

2024/04/11 20:42:24 testTwoCandidateForElection PASS

2024/04/11 20:42:29 testSplitVote PASS

2024/04/11 20:42:33 testAllForElection PASS

2024/04/11 20:42:36 testLeaderRevertToFollower PASS

2024/04/11 20:42:42 testOneSimplePut FAIL

2024/04/11 20:42:48 testOneSimpleUpdate PASS

2024/04/11 20:42:55 testOneSimpleDelete PASS

2024/04/11 20:43:02 testDeleteNonExistKey PASS