# FILO2<sup>x</sup>

FILO2<sup>x</sup> is a shared-memory parallel implementation of [FILO2](https://github.com/acco93/filo2) using x solvers to cooperatively and asynchronously optimize the same underlying solution without the imposition of any guidance or rigid boundaries on the solvers.

Communication among solvers happens indirectly through message passing handled by an additional dispatcher component collecting and broadcasting solver messages.

![architecture](imgs/highlevel.png?raw=true)

Every solver works on its copy of the reference solution and performs sequence of synchronization and generation stages.

![architecture](imgs/solver.png?raw=true)

During the generation stage, ruin, recreate, and local search are applied to the solver reference solution to generate a neighbor solution. 
The (usually small) difference between the neighbor and the reference solution, called candidate change, is captured into a message and sent to the dispatcher. The dispatcher manages changes in FIFO order and simply broadcasts them to all the available solvers.
After sending the message, the solver reverts the change to restore the original reference solution.

During the synchronization stage a solver applies some of the candidate changes received by the dispatcher. Given that solvers receive messages in the same order, they all traverse the same search trajectory and they will eventually converge to the same final solution. However, at any given time, they may be at different different points of it, and thus have a different reference solution. Candidate changes that when applied do not lead to feasible solutions are discarded.

TODO: add code & link to report
