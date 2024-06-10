<template>
    <div>
        <button @click="toggleForm('income')">수입</button>
        <button @click="toggleForm('expense')">지출</button>

        <div v-if="showIncomeForm">
            <h2>수입 입력</h2>
            <form @submit.prevent="addIncome">
                <label for="incomeAmount">금액:</label>
                <input type="number" id="incomeAmount" v-model="incomeAmount" required />
                <button type="submit">저장</button>
            </form>
        </div>

        <div v-if="showExpenseForm">
            <h2>지출 입력</h2>
            <form @submit.prevent="addExpense">
                <label for="expenseAmount">금액:</label>
                <input type="number" id="expenseAmount" v-model="expenseAmount" required />
                <button type="submit">저장</button>
            </form>
        </div>

        <h2>수입 내역</h2>
        <ul>
            <li v-for="(income, index) in incomes" :key="index">{{ income }}</li>
        </ul>

        <h2>지출 내역</h2>
        <ul>
            <li v-for="(expense, index) in expenses" :key="index">{{ expense }}</li>
        </ul>
    </div>
</template>

<script>
export default {
    data() {
        return {
            showIncomeForm: false,
            showExpenseForm: false,
            incomeAmount: '',
            expenseAmount: '',
            incomes: [],
            expenses: [],
        };
    },
    methods: {
        toggleForm(type) {
            if (type === 'income') {
                this.showIncomeForm = true;
                this.showExpenseForm = false;
            } else {
                this.showIncomeForm = false;
                this.showExpenseForm = true;
            }
        },
        addIncome() {
            this.incomes.push(this.incomeAmount);
            this.incomeAmount = '';
        },
        addExpense() {
            this.expenses.push(this.expenseAmount);
            this.expenseAmount = '';
        },
    },
};
</script>
