# Gate based maxcut solver

The code below allows, given a graph, to obtain the maxcut of that graph.

The method consists of starting from a Hamiltonian that does not correspond to our graph but rather to a placement of the graph's points independently of each other; this is what we represent by the Hamiltonian $H_x$. Then, we construct an interaction Hamiltonian that maps the connections between the nodes of our graph, which we call the Hamiltonian $H_c$. The Hamiltonian $H_x$ has a well-known ground state. This method involves initializing the system in the ground state of the Hamiltonian $H_x$, then using the parameters $\eta$ to slowly evolve the Hamiltonian $H_{x}$ towards the Hamiltonian $H_c$. Since the evolution is slow, this ensures that the system remains in the ground state throughout the procedure, and thus, in the end, the system is in the ground state of the Hamiltonian associated with our graph $H_c$.

### The code

1. The first step is to implement the graph. We use the adjacency matrix of the graph. Since graphs are assumed to be undirected with a single weight, the adjacency matrix is symmetric and contains only 1s. This matrix is created using the randm_matrix function.

2. I create the function ``u_x`` which is the evolution operator of the Hamiltonian hx.

3. I create the function ``u_c`` which is the evolution operator of the Hamiltonian hc.

4. I create the function ``U_init()`` which creates a circuit that returns the ground state of hx.

5. I create the function u_n which applies the Suzuki-Trotter approximation of $U_{\eta}$. We notice that at each iteration, as $\frac{j - 1}{m(\eta -1)}$ increases, $\frac{\eta - j}{m(\eta -1)}$ decreases, which reflects the transition from the Hamiltonian hx to hc.

6. The function qaoa_circuit simply initializes the circuit in the ground state of $H_{x}$ before applying ``u_n``.

7. The function ``ground_state_optimizer()`` in this function, I use the qaoa_circuit function with the evolve method to retrieve the state vector of our circuit after the evolution of our system: $$\ket{\Phi_{\eta,m}} = U_{\eta , m}U_{init}\ket{0}$$ Then I calculate c, which is the eigenvalue of this state vector: $$C = \bra{\Phi_{\eta,m}}H_{c}\ket{\Phi_{\eta,m}}$$ then we perform optimization on C to make it as small as possible and thus obtain a good approximation of the ground state $\ket{\Phi_{\eta,m}}$. The optimization doesn't seem to work very well, probably because the increments of $\eta$ and m are integers. I could solve this problem for m by changing the way I construct the u_n circuit. I could first obtain the matrix corresponding to $$U_{c}(\frac{j - 1}{m(\eta - 1)})U_{x}(\frac{\eta - j}{m(\eta - 1)})$$ Then apply the power m and transform the resulting matrix into an observable, and finally add it to the ``u_c`` circuit.

8. Since the method outputs several basis vectors whose modulus is sufficiently large, I calculate the corresponding maxcut and je rechier les plus grand.
   
9. The function `calculate_maxcut_from_strings()` executes the final task. At this stage, I already have the bit string that corresponds to the partition of the maxcut. In fact, the "0" bits form one partition and the "1" bits form the other. For example, if I have "0101" with the order "0123", then the nodes from zero, namely {0, 2}, form one partition and {1, 3} form the other partition. It only remains to determine the number of edges that connect these partitions, which is the maxcut.

Unfortunately, I couldn't find a simple Python function to estimate the maxcut for quick comparison.

Also, I notice a small bug at the end of execution. When I perform the following execution, I don't have any convergence. It's only after a third execution that I obtain results.
