<script>
    import axios from "axios";
    export let name;
    export let profilePicture;
    // export let subjects;
    export let subjectId;
    export let allotedTeachers;
    export let allotHours;
    let originalHours = allotHours;
    // console.log(originalHours);
    $: currentLoad =
        originalHours.lectureHrs +
        originalHours.practicalHrs +
        originalHours.tutorialHrs;
    export let id;
    let currentSubject;
    const allotTeacherUrl = "http://localhost:5000/api/general/commit_ltp";
    const saveHours = async () => {
        try {
            const resp = await axios.post(allotTeacherUrl, {
                teacher_id: id.toString(),
                subject_id: subjectId,
                lecture_hours: allotHours.lectureHrs,
                tutorial_hours: allotHours.tutorialHrs,
                practical_hours: allotHours.practicalHrs,
            });
            console.log(resp);

            let isAssigned = false;
            const subjectWorkload = resp.data.available_subject_workload;
            console.log(subjectWorkload);
            if (
                subjectWorkload.lecture == 0 &&
                subjectWorkload.tutorial == 0 &&
                subjectWorkload.practical == 0
            ) {
                isAssigned = true;
            }
            const updateSubjectUrl = "http://localhost:5000/api/subjects";
            try {
                const resp = await axios.post(updateSubjectUrl, {
                    id: subjectId,
                    isAssigned: isAssigned,
                });
                console.log(resp);
            } catch (err) {
                console.log(err);
            }
            originalHours = allotHours;
            // allotHours.lectureHrs = ;
        } catch (err) {
            alert(err.response.data.erro_desc.message);
            allotHours = originalHours;
        }
    };

    // console.log(allotHours);
    function handleLectures(e) {
        if (e.target.textContent == "+" && allotHours.lectureHrs < 8) {
            allotHours.lectureHrs += 1;
        } else if (e.target.textContent == "-" && allotHours.lectureHrs > 0) {
            allotHours.lectureHrs -= 1;
        }
    }
    function handleTutorials(e) {
        if (e.target.textContent == "+") {
            allotHours.tutorialHrs += 1;
        } else if (e.target.textContent == "-" && allotHours.tutorialHrs > 0) {
            allotHours.tutorialHrs -= 1;
        }
    }
    function handlePracticals(e) {
        if (e.target.textContent == "+") {
            allotHours.practicalHrs += 2;
        } else if (e.target.textContent == "-" && allotHours.practicalHrs > 0) {
            allotHours.practicalHrs -= 2;
        }
    }
</script>

<div class="mt-2 bg-white px-4 rounded-md pb-1">
    <div class="flex justify-between">
        <div>Current Hours</div>
        <div>{currentLoad}</div>
    </div>

    <div class="w-full mt-1">
        <img
            src={profilePicture}
            alt={name}
            class="w-20 h-20 rounded-full m-auto"
        />
    </div>
    <div class="text-center">{name}</div>
    <div class="flex flex-col gap-y-2">
        <div class="flex gap-x-2 items-center">
            <div class="w-14">Lecture</div>
            <button on:click={handleLectures}>-</button>
            <div>{allotHours.lectureHrs}</div>
            <button on:click={handleLectures}>+</button>
        </div>
        <div class="flex gap-x-2 items-center">
            <div class="w-14">Tutorial</div>
            <button on:click={handleTutorials}>-</button>
            <div>{allotHours.tutorialHrs}</div>
            <button on:click={handleTutorials}>+</button>
        </div>
        <div class="flex gap-x-2 items-center">
            <div class="w-14">Practical</div>
            <button on:click={handlePracticals}>-</button>
            <div>{allotHours.practicalHrs}</div>
            <button on:click={handlePracticals}>+</button>
        </div>
    </div>

    <button on:click={saveHours} class="w-full save">Save</button>
</div>

<style>
    button {
        background-color: #04aa6d;
        border: none;
        color: white;
        padding: 4px 8px;
        text-align: center;
        text-decoration: none;
        display: inline-block;
        font-size: 16px;
        margin: 4px 2px;
        border-radius: 2px;
    }

    .save {
        background-color: black;
        border-radius: 6px;
    }
</style>
