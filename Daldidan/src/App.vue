<template>
    <div id="app">
        <h1>달력 및 가계부</h1>
        <div>
            <label for="year">연도:</label>
            <input v-model.number="year" type="number" id="year" />
        </div>
        <div>
            <label for="month">월:</label>
            <select v-model.number="month" id="month">
                <option v-for="m in 12" :key="m" :value="m">{{ m }}월</option>
            </select>
        </div>
        <button @click="generateCalendar">달력 생성</button>

        <div v-if="days.length">
            <h2>{{ year }}년 {{ month }}월</h2>
            <div class="calendar">
                <div class="day-header" v-for="day in dayNames" :key="day">
                    {{ day }}
                </div>
                <div
                    class="day"
                    v-for="(day, index) in days"
                    :key="index"
                    @click="selectDay(day)"
                >
                    {{ day }}
                </div>
            </div>
        </div>

        <div v-if="selectedDate">
            <h2>{{ selectedDate }}의 수입 및 지출 내역</h2>
            <ul>
                <li
                    v-for="(transaction, index) in transactionsByDate"
                    :key="index"
                >
                    {{ transaction.type === 'income' ? '수입' : '지출' }}:
                    {{ transaction.amount }}
                </li>
            </ul>
        </div>

        <div>
            <label for="amount">금액:</label>
            <input v-model.number="amount" type="number" id="amount" />
        </div>
        <div>
            <label for="type">유형:</label>
            <select v-model="type" id="type">
                <option value="income">수입</option>
                <option value="expense">지출</option>
            </select>
        </div>
        <button @click="addTransaction">추가</button>

        <h2>전체 총 수입: {{ totalIncome }}</h2>
        <h2>전체 총 지출: {{ totalExpense }}</h2>
        <h2>전체 잔액: {{ balance }}</h2>
    </div>
</template>

<script>
export default {
    data() {
        return {
            year: new Date().getFullYear(),
            month: new Date().getMonth() + 1,
            days: [],
            dayNames: ['일', '월', '화', '수', '목', '금', '토'],
            transactions: {},
            selectedDate: null,
            amount: 0,
            type: 'income',
        };
    },
    computed: {
        totalIncome() {
            return this.calculateTotal(this.allTransactions, 'income');
        },
        totalExpense() {
            return this.calculateTotal(this.allTransactions, 'expense');
        },
        balance() {
            return this.totalIncome - this.totalExpense;
        },
        allTransactions() {
            return Object.values(this.transactions).flat();
        },
        transactionsByDate() {
            return this.transactions[this.selectedDate] || [];
        },
    },
    methods: {
        generateCalendar() {
            const date = new Date(this.year, this.month - 1, 1);
            const days = [];
            const firstDayIndex = date.getDay();
            const lastDay = new Date(this.year, this.month, 0).getDate();

            // 빈 칸 채우기
            for (let i = 0; i < firstDayIndex; i++) {
                days.push('');
            }

            // 날짜 채우기
            for (let i = 1; i <= lastDay; i++) {
                days.push(i);
            }

            this.days = days;
        },
        selectDay(day) {
            if (day) {
                this.selectedDate = `${this.year}-${String(this.month).padStart(
                    2,
                    '0'
                )}-${String(day).padStart(2, '0')}`;
            }
        },
        addTransaction() {
            if (this.selectedDate) {
                if (!this.transactions[this.selectedDate]) {
                    this.transactions[this.selectedDate] = [];
                }
                this.transactions[this.selectedDate].push({
                    amount: this.amount,
                    type: this.type,
                });
                this.amount = 0; // 입력 필드 초기화
            }
        },
        calculateTotal(transactions, type) {
            return transactions
                .filter((transaction) => transaction.type === type)
                .reduce((sum, transaction) => sum + transaction.amount, 0);
        },
    },
    mounted() {
        this.generateCalendar();
    },
};
</script>

<style>
#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
}

input,
select {
    margin: 5px;
}

button {
    margin: 10px;
}

.calendar {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 5px;
    margin-top: 20px;
}

.day-header {
    font-weight: bold;
}

.day {
    padding: 10px;
    border: 1px solid #ccc;
    text-align: center;
    cursor: pointer;
}

.day:hover {
    background-color: #f0f0f0;
}
</style>
