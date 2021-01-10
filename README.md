```kotlin
package com.paigesoftware.incrementtimer

import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import kotlinx.android.synthetic.main.activity_main.*
import kotlinx.coroutines.CoroutineScope
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.launch
import java.util.*
import kotlin.math.roundToInt

class MainActivity : AppCompatActivity() {

    private val gTimer = Timer()
    private var gCurrentTime: Double = 0.0
    private var gTimerIsRunning = false
    private var gTimerTask: TimerTask? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        button_start.setOnClickListener {
            startTimer()
        }

        button_pause.setOnClickListener {
            pauseTimer()
        }

        button_reset.setOnClickListener {
            resetTimer()
        }

    }

    private fun startTimer() {
        if(!gTimerIsRunning) {
            gTimerTask = object: TimerTask() {
                override fun run() {
                    CoroutineScope(Dispatchers.Main).launch {
                        gCurrentTime++
                        timerText.text = updateTimerText()
                    }
                }
            }
            gTimer.scheduleAtFixedRate(gTimerTask, 0, 1000)
            gTimerIsRunning = true
        }
    }

    private fun pauseTimer() {
        if(gTimerIsRunning) {
            gTimerTask?.cancel()
            gTimerIsRunning = false
        }
    }

    private fun resetTimer() {
        gTimerTask?.cancel()
        gTimerIsRunning = false
        gCurrentTime = 0.0
        timerText.text = updateTimerText()
    }

    private fun updateTimerText(): String {
        val rounded = gCurrentTime.roundToInt()
        val seconds = ((rounded % 86400) % 3600) % 60
        val minutes = ((rounded % 86400) % 3600) / 60
        val hours = ((rounded % 86400) / 3600)
        return String.format(Locale.getDefault(),"%02d:%02d:%02d", hours, minutes, seconds)
    }

}

```