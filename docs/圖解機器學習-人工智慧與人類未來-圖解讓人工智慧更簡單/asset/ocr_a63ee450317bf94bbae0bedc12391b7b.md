In order to cast the problem in a form for recursive estimation, we let the weights in the network constitute the state $x$ of the nonlinear system, i.e.

\(x = [\theta_{1,2}^l, \theta_{1,3}^l \cdots \theta_{N(0),N(1)}^l]^T.\) (12)

The vector \(x\) thus consists of all weights arranged in a linear array with dimension equal to the total number of weights \(M\) in the system. The system model thus is

\(x(n+1) = x(n) \quad n > 0,\) (13)
\(d(n) = z^L(n) + v(n) = h_n(x(n), z^0(n)) + v(n),\) (14)

where at time \(n\), \(z^0(n)\) is the input vector from the training set, \(d(n)\) is the corresponding desired output vector, and \(z^L(n)\) is the output vector produced by the net. The components of \(h_n(\cdot)\) define the nonlinear relationships between the inputs, weights and outputs of the net. If \(\Gamma(\cdot)\) is the nonlinearity used, then \(z^L(n) = h_n(x(n), z^0(n))\) is given by

\(z^L(n) = \Gamma\{(\theta^L)^T \Gamma\{(\theta^{L-1})^T \Gamma \cdots \Gamma\{(\theta^1)^T z^0(n)\\} \cdots \}\},\) (15)

where \(\Gamma\) applies componentwise to vector arguments. Note that the input vectors appear only implicitly through the observation function \(h_n(\cdot)\) in (14). The initial state (before training) \(x(0)\) of the network is defined by populating the net with gaussian random variables with a \(N(\bar{x}(0), P(0))\) distribution where \(\bar{x}(0)\) and \(P(0)\) reflect any apriori knowledge about the weights. In the absence of any such knowledge, a \(N(0, 1/\epsilon I)\) distribution can be used, where \(\epsilon\) is a small number and \(I\) is the identity matrix. For the system in (13) and (14), the extended Kalman filter recursion simplifies to

\(\hat{x}(n+1) = \hat{x}(n) + K(n)[d(n) - h_n(\hat{x}(n), z^0(n))]\) (16)
\(K(n) = P(n)H(n)[R(n) + H^T(n)P(n)H(n)]^{-1}\) (17)
\(P(n+1) = P(n) - K(n)H^T(n)P(n)\) (18)

where \(P(n)\) is the (approximate) conditional error covariance matrix.

Note that (16) is similar to the weight update equation in back-propagation with the last term \([z^L - h_n(\hat{x}, z^0)]\) being the error at the output layer. However, unlike the delta rule used in back-propagation, this error is propagated to the weights through the Kalman gain \(K(n)\) which updates each weight through the entire gradient matrix \(H(n)\) and the conditional error covariance matrix \(P(n)\). In this sense, the Kalman algorithm is not a local training algorithm. However, the inversion required in (17) has dimension equal to the number of outputs \(l\), not the number of weights \(M\), and thus does not grow as weights are added to the problem.

# EXAMPLES AND RESULTS

To evaluate the output and the convergence properties of the extended Kalman algorithm. we constructed mappings using two-dimensional inputs with two or four outputs as shown in Fig. 1. Limiting the input vector to 2 dimensions allows us to visualize the decision regions obtained by the net and