---
layout: default
title: RIFT Request Test
---



<h1>EC2 Instance Information</h1>

<button id="fetchData" onclick="fetchEC2Data()">Fetch EC2 Instance Data</button>
<div id="ec2Data"></div>

<script>
    function fetchEC2Data() {
        fetch('riftflask.stu.nighthawkcodingsociety.com/get-ec2-instances', {
            method: 'GET',
            headers: {
                'Content-Type': 'application/json',
            },
        })
        .then(response => response.json())
        .then(data => {
            document.getElementById('ec2Data').innerHTML = data;
        })
        .catch((error) => {
            console.error('Error:', error);
            document.getElementById('ec2Data').innerHTML = 'Error fetching data';
        });
    }
</script>




