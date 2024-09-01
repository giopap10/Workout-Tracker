<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Workout Tracker</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #2c2c2c;
            color: #e0e0e0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
        }
        .container {
            background: rgba(40, 40, 40, 0.9);
            padding: 20px;
            border-radius: 20px;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.4);
            width: 500px;
            max-width: 90%;
        }
        .header {
            display: flex;
            justify-content: center;
            align-items: center;
            margin-bottom: 20px;
        }
        .header h2 {
            font-size: 24px;
            margin: 0;
            color: #e0e0e0;
        }
        .exercises, .rewards {
            margin-top: 20px;
        }
        .exercise, .reward {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 10px;
            font-size: 18px;
            background: #3b3b3b;
            padding: 10px;
            border-radius: 10px;
        }
        .exercise input, .reward input {
            width: 80px;
            text-align: right;
            background: #4a4a4a;
            border: 1px solid #666;
            color: #e0e0e0;
            border-radius: 5px;
        }
        .exercise .actions, .reward .actions {
            display: flex;
            gap: 5px;
        }
        .exercise .actions button, .reward .actions button {
            background-color: #ff4500;
            border: none;
            color: white;
            padding: 5px 10px;
            cursor: pointer;
            border-radius: 5px;
            font-size: 14px;
        }
        .exercise .actions button.edit, .reward .actions button.edit {
            background-color: #ffa500;
        }
        .exercise .actions button:hover, .reward .actions button:hover {
            opacity: 0.8;
        }
        button {
            margin-top: 10px;
            padding: 10px;
            width: 100%;
            background-color: #444;
            color: #fff;
            border: none;
            border-radius: 10px;
            cursor: pointer;
            font-size: 16px;
        }
        button:hover {
            background-color: #666;
        }
        .reward-button {
            background-color: #4caf50;
        }
        .reward-button:hover {
            background-color: #45a049;
        }
        .hidden {
            display: none;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h2>Workout Tracker</h2>
        </div>
        <div class="exercises" id="exercises">
            <!-- Exercises will be dynamically added here -->
        </div>
        <button onclick="resetDailyProgress()">Reset Daily Progress</button>
        <div class="rewards" id="rewards">
            <!-- Rewards will be dynamically added here -->
        </div>
        <button class="reward-button" id="chooseRewardButton" onclick="chooseReward()">Choose Reward</button>
        <button onclick="addReward()">Add Reward</button>
    </div>

    <script>
        // Weekly training program
        const weeklyExercises = {
            'Monday': [
                { name: "Warm-up: Jump Rope", sets: "", reps: "10 minutes" },
                { name: "Barbell Squats", sets: 4, reps: "8-10 reps" },
                { name: "Dumbbell Bench Press", sets: 4, reps: "8-10 reps" },
                { name: "Bent Over Barbell Rows", sets: 4, reps: "8-10 reps" },
                { name: "Dumbbell Lunges", sets: 4, reps: "10 reps per leg" },
                { name: "Dumbbell Shoulder Press", sets: 3, reps: "10-12 reps" },
                { name: "Finisher: HIIT Cardio", sets: "", reps: "10 minutes" }
            ],
            'Tuesday': [
                { name: "Warm-up: Shadowboxing", sets: "", reps: "10 minutes" },
                { name: "Heavy Bag Work", sets: "", reps: "1 hour" },
                { name: "Bodyweight Circuit", sets: "3 rounds", reps: "1 min each exercise, 30 sec rest" },
                { name: "Exercises: Push-ups, Jump Squats, Burpees, Mountain Climbers, Bicycle Crunches", sets: "", reps: "" },
                { name: "Finisher: Heavy Bag Strikes", sets: "10 rounds", reps: "30 sec each, 30 sec rest" }
            ],
            'Wednesday': [
                { name: "Warm-up: Jump Rope or Dynamic Stretches", sets: "", reps: "10 minutes" },
                { name: "Barbell Deadlifts", sets: 4, reps: "8-10 reps" },
                { name: "Dumbbell Step-ups", sets: 4, reps: "10 reps per leg" },
                { name: "Goblet Squats", sets: 4, reps: "12 reps" },
                { name: "Barbell Romanian Deadlifts", sets: 4, reps: "10-12 reps" },
                { name: "Finisher: Sprints or Jump Rope", sets: "", reps: "10 x 100m sprints or high-intensity jump rope (1-minute rest)" }
            ],
            'Thursday': [
                { name: "Light Stretching or Yoga", sets: "", reps: "30-45 minutes" },
                { name: "Optional: Light Cardio", sets: "", reps: "30 minutes walk or swim" }
            ],
            'Friday': [
                { name: "Warm-up: Shadowboxing or Jump Rope", sets: "", reps: "10 minutes" },
                { name: "Barbell Overhead Press", sets: 4, reps: "8-10 reps" },
                { name: "Dumbbell Rows", sets: 4, reps: "10 reps per arm" },
                { name: "Dumbbell Bicep Curls", sets: 4, reps: "12 reps" },
                { name: "Dumbbell Tricep Extensions", sets: 4, reps: "12 reps" },
                { name: "Finisher: Push-up Variations", sets: "3 rounds", reps: "Diamond, Wide, Explosive" }
            ],
            'Saturday': [
                { name: "Warm-up: Jump Rope or Shadowboxing", sets: "", reps: "10 minutes" },
                { name: "Heavy Bag Work", sets: "", reps: "1 hour" },
                { name: "Conditioning Circuit", sets: "3 rounds", reps: "1 min each exercise, 30 sec rest" },
                { name: "Exercises: Burpees, Jump Lunges, High Knees, Kettlebell Swings", sets: "", reps: "" },
                { name: "Finisher: Shadowboxing", sets: "5 rounds", reps: "3 minutes each with 30 sec rest" }
            ],
            'Sunday': [
                { name: "Rest Day", sets: "", reps: "Complete rest or light stretching" }
            ]
        };

        function getTodayDay() {
            const today = new Date();
            const daysOfWeek = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];
            return daysOfWeek[today.getDay()];
        }

        function loadData() {
            const todayDay = getTodayDay();
            const exercises = JSON.parse(localStorage.getItem('exercises') || '[]');
            const rewards = JSON.parse(localStorage.getItem('rewards') || '[]');

            // Display today's exercises
            const todayExercises = weeklyExercises[todayDay];
            todayExercises.forEach(exercise => {
                addExerciseToDOM(exercise);
            });

            rewards.forEach(reward => {
                addRewardToDOM(reward);
            });
        }

        function saveData() {
            const exercises = [];
            document.querySelectorAll('.exercise').forEach(exercise => {
                const name = exercise.querySelector('span').textContent;
                const sets = exercise.querySelector('input[name="sets"]').value;
                const reps = exercise.querySelector('input[name="reps"]').value;
                const progress = exercise.querySelector('input[name="progress"]').value;
                exercises.push({ name, sets, reps, progress });
            });

            const rewards = [];
            document.querySelectorAll('.reward').forEach(reward => {
                const name = reward.querySelector('span').textContent;
                rewards.push(name);
            });

            localStorage.setItem('exercises', JSON.stringify(exercises));
            localStorage.setItem('rewards', JSON.stringify(rewards));
        }

        function addExerciseToDOM(exercise) {
            const exerciseContainer = document.getElementById("exercises");
            const exerciseId = Date.now();
            const newExercise = document.createElement("div");
            newExercise.className = "exercise";
            newExercise.id = `exercise-${exerciseId}`;
            newExercise.innerHTML = `
                <span>${exercise.name}</span>
                <input type="number" name="sets" value="${exercise.sets}" min="0" readonly>
                Sets
                <input type="text" name="reps" value="${exercise.reps}" readonly>
                Reps
                <input type="number" name="progress" value="${exercise.progress || 0}" min="0" max="${exercise.sets * exercise.reps}">
                /${exercise.sets * exercise.reps}
                <div class="actions">
                    <button class="edit hidden" onclick="toggleEditExercise(${exerciseId})">Edit</button>
                    <button class="delete" onclick="deleteExercise(${exerciseId})">Delete</button>
                </div>
            `;
            exerciseContainer.appendChild(newExercise);
        }

        function deleteExercise(exerciseId) {
            document.getElementById(`exercise-${exerciseId}`).remove();
            saveData();
        }

        function toggleEditExercise(exerciseId) {
            const exerciseElement = document.getElementById(`exercise-${exerciseId}`);
            const editButton = exerciseElement.querySelector('.edit');
            if (editButton.classList.contains('hidden')) {
                editButton.classList.remove('hidden');
            } else {
                editButton.classList.add('hidden');
                const currentName = exerciseElement.querySelector('span').textContent;
                const currentSets = exerciseElement.querySelector('input[name="sets"]').value;
                const currentReps = exerciseElement.querySelector('input[name="reps"]').value;
                const newName = prompt("Enter new exercise name:", currentName);
                const newSets = prompt("Enter new sets:", currentSets);
                const newReps = prompt("Enter new reps:", currentReps);
                if (newName && newSets && newReps) {
                    exerciseElement.querySelector('span').textContent = newName;
                    exerciseElement.querySelector('input[name="sets"]').value = newSets;
                    exerciseElement.querySelector('input[name="reps"]').value = newReps;
                    exerciseElement.querySelector('input[name="progress"]').max = newSets * newReps; // Update max progress
                    exerciseElement.querySelector('input[name="progress"]').value = 0; // Reset progress
                }
                saveData();
            }
        }

        function addReward() {
            const rewardName = prompt("Enter reward name:");
            if (rewardName) {
                addRewardToDOM(rewardName);
                saveData();
            }
        }

        function addRewardToDOM(rewardName) {
            const rewardContainer = document.getElementById("rewards");
            const rewardId = Date.now();
            const newReward = document.createElement("div");
            newReward.className = "reward";
            newReward.id = `reward-${rewardId}`;
            newReward.innerHTML = `
                <span>${rewardName}</span>
                <div class="actions">
                    <button class="edit hidden" onclick="toggleEditReward(${rewardId})">Edit</button>
                    <button class="delete" onclick="deleteReward(${rewardId})">Delete</button>
                </div>
            `;
            rewardContainer.appendChild(newReward);
        }

        function deleteReward(rewardId) {
            document.getElementById(`reward-${rewardId}`).remove();
            saveData();
        }

        function toggleEditReward(rewardId) {
            const rewardElement = document.getElementById(`reward-${rewardId}`);
            const editButton = rewardElement.querySelector('.edit');
            if (editButton.classList.contains('hidden')) {
                editButton.classList.remove('hidden');
            } else {
                editButton.classList.add('hidden');
                const newName = prompt("Enter new reward name:", rewardElement.querySelector('span').textContent);
                if (newName) {
                    rewardElement.querySelector('span').textContent = newName;
                    saveData();
                }
            }
        }

        function chooseReward() {
            const allCompleted = Array.from(document.querySelectorAll('.exercise')).every(exercise => {
                const input = exercise.querySelector('input[name="progress"]');
                const goal = input.max;
                const progress = input.value;
                return progress >= goal;
            });

            if (allCompleted) {
                const rewards = Array.from(document.querySelectorAll('.reward')).map(reward => reward.querySelector('span').textContent);
                if (rewards.length > 0) {
                    const rewardChoice = prompt(`Choose your reward:\n${rewards.join('\n')}`);
                    if (rewards.includes(rewardChoice)) {
                        alert(`Congratulations! You have chosen: ${rewardChoice}`);
                        document.getElementById('chooseRewardButton').disabled = true;
                        saveData();
                    } else {
                        alert('Invalid reward choice.');
                    }
                } else {
                    alert('No rewards available.');
                }
            } else {
                alert('Complete all exercises to choose a reward.');
            }
        }

        function resetDailyProgress() {
            document.querySelectorAll('.exercise input[name="progress"]').forEach(input => {
                input.value = 0;
            });
            saveData();
        }

        // Initialize the application
        loadData();
    </script>
</body>
</html>
