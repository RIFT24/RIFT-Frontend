---
layout: default
title: RIFT Frontend
---

<style>
    .server-status {
        display: flex;
        justify-content: center;
        gap: 20px;
        margin-top: 50px;
    }

    .server {
        width: 150px;
        height: 150px;
        background-color: grey;
        border-radius: 10px;
        display: flex;
        align-items: center;
        justify-content: center;
        flex-direction: column;
        color: white;
        font-family: Arial, sans-serif;
        box-shadow: 0 4px 8px rgba(0,0,0,0.2);
        position: relative; /* For absolute positioning of status icon */
    }

    .server-name {
        font-size: 20px;
        margin-bottom: 10px;
    }

    .status-icon {
        height: 20px;
        width: 20px;
        border-radius: 50%;
        display: inline-block;
        position: absolute;
        top: 10px;
        right: 10px;
    }

    .online {
        background-color: #28a745;
    }

    .offline {
        background-color: #dc3545;
    }

    .maintenance {
        background-color: #ffc107;
    }

    .details-container {
        display: flex;
        flex-direction: row;
        justify-content: space-around;
        flex-wrap: wrap;
        margin-top: 30px;
    }

    .server-card {
        background-color: #f2f2f2;
        border-radius: 10px;
        box-shadow: 0 4px 8px rgba(0,0,0,0.2);
        padding: 20px;
        margin: 10px;
        width: 800px;
        font-family: monospace;
    }

    .server-title {
        font-size: 20px;
        font-weight: bold;
        margin-bottom: 15px;
    }

    .server-stats {
        white-space: pre-wrap;
        word-break: break-word;
    }
</style>

<body>

<div class="server-status">
    <div class="server">
        <span class="status-icon" id="statusIconRiftP1"></span>
        <div class="server-name">RIFT P1</div>
        <div class="server-status-text" id="statusTextRiftP1">Offline</div>
    </div>
    <div class="server">
        <span class="status-icon" id="statusIconRiftP3"></span>
        <div class="server-name">RIFT P3</div>
        <div class="server-status-text" id="statusTextRift31">Offline</div>
    </div>
    <div class="server">
        <span class="status-icon" id="statusIconRiftDev"></span>
        <div class="server-name">RIFT Dev</div>
        <div class="server-status-text" id="statusTextRiftDev">Offline</div>
    </div>
</div>

<div class="details-container">
    <div class="server-card">
        <div class="server-title" id="riftP1Title">RIFT P1 / RIFT-CSA-P1</div>
        <div class="server-stats" id="riftP1Stats">
            <!-- Stats will be filled here -->
        </div>
    </div>
    <div class="server-card">
        <div class="server-title" id="riftP1Title">RIFT P3 / RIFT-CSA-P3</div>
        <div class="server-stats" id="riftP1Stats">
            <!-- Stats will be filled here -->
        </div>
    </div>
    <div class="server-card">
        <div class="server-title" id="riftDevTitle">RIFT Dev / RIFT_Dev</div>
        <div class="server-stats" id="riftDevStats">
            <!-- Stats will be filled here -->
        </div>
    </div>
    <!-- Repeat for other servers as needed -->
</div>

</body>
<script>
    window.onload = function() {
        // Update server information immediately on page load
        updateServerInformation();

        // Then continue to update every 5 minutes
        setInterval(updateServerInformation, 300000); // 300000 milliseconds = 5 minutes
    };

    function updateServerInformation() {
        fetch('https://riftflask.stu.nighthawkcodingsociety.com/get-ec2-instances')
        .then(response => response.json())
        .then(data => {
            const instances = data.Reservations.flatMap(reservation => reservation.Instances);
            for (let instance of instances) {
                if (instance.InstanceId === 'i-019caecd05b459160') {
                    document.getElementById('riftP1Stats').innerHTML = formatInstanceData(instance);
                }
                if (instance.InstanceId === 'i-062cc156f36712677') {
                    document.getElementById('riftP3Stats').innerHTML = formatInstanceData(instance);
                }
                if (instance.InstanceId === 'i-07494ecf4435591be') {
                    document.getElementById('riftDevStats').innerHTML = formatInstanceData(instance);
                }
                updateServerStatus(instance);
                // Repeat for other instances
            }
        })
        .catch(error => {
            console.error('Error fetching EC2 instance data:', error);
        });
    }

    function formatInstanceData(instance) {
        const coreCount = instance.CpuOptions.CoreCount;
        const imageId = instance.ImageId;
        const instanceId = instance.InstanceId;
        const securityGroups = instance.SecurityGroups.map(group => `${group.GroupName} (${group.GroupId})`).join(', ');
        const platformDetails = instance.PlatformDetails;

        return `
            System information as of ${new Date().toUTCString()}

            Core Count: ${coreCount}
            Image ID: ${imageId}
            Instance ID: ${instanceId}
            Security Groups: ${securityGroups}
            Platform Details: ${platformDetails}

            AWS public IP: ${instance.PublicIpAddress || 'N/A'}
        `;
    }
    function updateServerStatus(instance) {
        let statusIconElement, statusTextElement;
        if (instance.InstanceId === 'i-019caecd05b459160') {
            statusIconElement = document.getElementById('statusIconRiftP1');
            statusTextElement = document.getElementById('statusTextRiftP1');
        }
        if (instance.InstanceId === 'i-062cc156f36712677') {
            statusIconElement = document.getElementById('statusIconRiftP3');
            statusTextElement = document.getElementById('statusTextRiftP3');
        }
        if (instance.InstanceId === 'i-07494ecf4435591be') {
            statusIconElement = document.getElementById('statusIconRiftDev');
            statusTextElement = document.getElementById('statusTextRiftDev');
        }
        // Repeat for other instances

        if (statusIconElement && statusTextElement) {
            if (instance.State.Name === 'running') {
                statusIconElement.className = 'status-icon online';
                statusTextElement.innerText = 'Online';
            } else {
                statusIconElement.className = 'status-icon offline';
                statusTextElement.innerText = 'Offline';
            }
        }
    }
</script>
