# Using-ClWatching-for-Resource-Monitoring-Create-CloudWatch-Alarms-and-Dashboards
Showcasing a lab that was done using AWS SNS, Cloudwatch, and EC2


# AWS CloudWatch Monitoring Lab

A hands-on AWS lab where I set up real-time EC2 monitoring using CloudWatch, SNS email alerts, and a live dashboard — then actually triggered the alarm by stressing the CPU.

---

## What This Lab Does

This project walks through building a basic but functional monitoring setup on AWS. You spin up an EC2 instance, configure CloudWatch to watch its CPU, hook up email notifications through SNS, and then intentionally break things to see if it all works. It's a good introduction to how cloud monitoring actually fits together in practice.

---

## The Problem It Solves

If you're running servers in the cloud, you need to know when something's wrong — before your users do. This lab simulates that scenario in miniature: what happens when CPU spikes? Does anyone get notified? Can you see it on a dashboard after the fact?

It's not a production setup, but it mirrors the logic of one.

---

## Main Steps

1. **Launch an EC2 Instance** (`MyEC2Server`) in the N. Virginia region using Amazon Linux 2023, t2.micro, and a new key pair.
2. **SSH in and install `stress-ng`** — a tool that can artificially max out CPU usage on demand.
3. **Create an SNS Topic** (`MyServerMonitor`) and subscribe your email to it, so you actually get notified when something trips.
4. **Set up a CloudWatch Alarm** that watches `CPUUtilization` and fires when it goes above 30% for more than 1 minute.
5. **Trigger the alarm** by running `stress-ng --cpu 10 --timeout 400s` and watching the CPU hit 100%.
6. **Check your inbox** — you should get an alert email from AWS with alarm details.
7. **Build a CloudWatch Dashboard** to visualize the CPU spikes visually over time.

---

## Key Components

| Component | What It Does |
|---|---|
| **EC2 Instance** | The thing being monitored. Runs Amazon Linux 2023, t2.micro. |
| **stress-ng** | CLI tool that hammers the CPU artificially. Used to trigger the alarm on purpose. |
| **SNS Topic (`MyServerMonitor`)** | The notification pipeline. Gets triggered by CloudWatch and sends you an email. |
| **CloudWatch Alarm** | Watches CPUUtilization every minute. Fires if it stays above 30%. |
| **CloudWatch Dashboard** | A simple line graph showing CPU over time — useful for seeing the spikes after the fact. |

---

## Challenges and Things I Learned

Getting the timing right was the trickiest part. CloudWatch takes 5–10 minutes after an EC2 instance launches before it starts showing metrics — so if you jump straight to creating the alarm, the instance won't show up in the search. You just have to wait.

The stress test itself was satisfying. Running `top` in a second terminal and watching `%Cpu(s)` jump to 100 makes the whole thing feel real. It's one thing to read about CPU monitoring; it's another to watch it happen and then get an email about it 60 seconds later.

I also hadn't thought much about the SNS subscription confirmation step before. You have to actually click the link in the email AWS sends — if you skip it, you won't receive alarm notifications.

---

## Conclusion

This lab is a solid introduction to the CloudWatch + SNS combo that shows up constantly in real AWS environments. The pieces aren't complicated individually, but connecting them in the right order — and understanding *why* each piece exists — is what makes it useful. If I were to extend this, I'd add alarms for memory and disk usage too, since CPU alone only tells part of the story.
