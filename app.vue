<script setup>
const visible = ref(false)
const dates = ref([new Date(), new Date()])
const dateOffset = ref(0)
const options = ref([
    { name: '±0', value: 0 },
    { name: '±1', value: 1 },
    { name: '±3', value: 3 },
    { name: '±5', value: 5 },
    { name: '±7', value: 7 },
    { name: '±15', value: 15 }
])

const startDate = computed(() => {
    return formatDate(dates.value[0])
})

const endDate = computed(() => {
    return formatDate(dates.value[1])
})

const formatDate = (date) => {
    const day = String(date.getDate()).padStart(2, '0')
    const month = String(date.getMonth() + 1).padStart(2, '0')
    const year = date.getFullYear()

    return `${day}/${month}/${year}`
}

const calculateDateOffset = (date, days) => {
    const result = new Date(date)
    result.setDate(result.getDate() + days)
    return result
}

const resetRangeSelection = () => {
    dates.value[1] = dates.value[0]
    dateOffset.value = 0
}

const calculateDates = (selectedDay = dates.value[0]) => {
    dates.value = [
        calculateDateOffset(selectedDay, dateOffset.value * -1),
        calculateDateOffset(selectedDay, dateOffset.value)
    ]
}

function getMidpointDate(startDate, endDate) {
    const start = new Date(startDate)
    const end = new Date(endDate)
    const midPoint = new Date(
        start.getTime() + (end.getTime() - start.getTime()) / 2
    )
    return midPoint
}

watch(dateOffset, () => {
    calculateDates(getMidpointDate(dates.value[0], dates.value[1]))
})
const date = ref()
</script>
<template>
    <div class="flex flex-col justify-center items-center h-screen w-screen">
        <div class="w-1/2 mb-12 flex justify-center">
            <Button label="Select dates" @click="visible = true" />
        </div>
        <div class="w-1/2 flex justify-center">
            <FloatLabel class="me-4">
                <label for="start_date">Start date</label>
                <InputText v-model="startDate" id="start_date" />
            </FloatLabel>
            <FloatLabel>
                <label for="end_date">End date</label>
                <InputText v-model="endDate" id="end_date" />
            </FloatLabel>
        </div>
    </div>

    <Dialog v-model:visible="visible" modal header="Select dates" class="w-1/4">
        <div class="flex w-full justify-center mb-4">
            <SelectButton
                v-model="dateOffset"
                :options="options"
                optionLabel="name"
                option-value="value" />
        </div>
        <DatePicker
            v-model="dates"
            selectionMode="range"
            inline
            class="w-full"
            @date-select="resetRangeSelection()"
            :minDate="new Date()" />
    </Dialog>
</template>
