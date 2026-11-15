
# Chapter 4: Using GPT Like an Expert

Answers stop imagination; questions drive content based on probabilities and parameter weights) also determine that its answers have a vagueness similar to humans.

The second challenge we encounter when using GPT is: Is the answer it gives correct? Not necessarily, so we must verify it ourselves.

### Prompt Example

10 teachers take 210 students on a study tour. A large bus seats 50 people and costs 400 yuan; a small bus seats 35 people and costs 300 yuan. How should they rent buses to save the most money? What is the minimum amount they need to pay?

(This is an incorrect answer) The most cost-effective plan is to rent 4 large buses and 2 small buses, totaling 2200 yuan, calculated as `4×400+2×300=2200`. Specific allocation: the first three large buses each carry 50 people, totaling 150 people; the fourth large bus carries 40 people, plus the remaining 10 people, totaling 50 people; two small buses each carry 35 people, totaling 70 people. Thus, 10 teachers and 210 students can be accommodated, and the rental cost is minimal at 2200 yuan.

(Answer after adding "step by step" to the prompt, this is an incorrect answer) First, determine how many buses need to be rented in total, which can be calculated using the following formula: Total people ÷ Seating capacity per bus = Number of buses needed. `210÷50=4.2`, round up to 5, need to rent 5 large buses. 10 teachers also need to ride, so 215 people in total need seating. The remaining 5 people can take a small bus. Therefore, a total of 6 buses need to be rented, including 5 large buses and 1 small bus. Therefore, the total rent to be paid is 2300 yuan, calculated as `2000+300=2300`, which is the most cost-effective rental plan.

(Correct answer, this is the solution derived from elementary school logic) To solve this problem, we can first try using large buses to meet everyone's needs. Then, we can try reducing the number of large buses while increasing the number of small buses to find the most cost-effective combination.

1. Using large buses: We need 5 large buses to meet the demand for 220 seats. Rent is 2000 yuan, calculated as `5×400=2000`.

2. Reduce 1 large bus, add 1 small bus: We use 4 large buses and 1 small bus, totaling 235 seats, rent is 1900 yuan.

3. Continue reducing 1 large bus, adding 1 small bus: Using 3 large buses and 2 small buses, totaling 220 seats, rent is 1800 yuan.

We found that the combination of 3 large buses and 2 small buses exactly meets everyone's needs and has the lowest rent. Therefore, the most cost-effective way is to rent 3 large buses and 2 small buses, with a minimum payment of 1800 yuan.

