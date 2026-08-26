# Tip1: how to trace the optimized out variables?
When debugging the embedded C program, we may face some **optimized out** variables due to the optimization level.

To prevent this, we can make them *global volatile*.


Example:
```c
/* Watch these variables in the debugger. */
volatile ssi_ret_t g_open_result;
volatile ssi_ret_t g_start_result;

volatile ssi_ret_t g_mute_minus1_result;  /* Test 6-1-1 / 6-1-5 */
volatile ssi_ret_t g_mute_0_result;       /* Test 6-1-2 / 6-1-6 */
volatile ssi_ret_t g_mute_1_result;       /* Test 6-1-3 / 6-1-7 */
volatile ssi_ret_t g_mute_2_result;       /* Test 6-1-4 / 6-1-8 */

volatile ssi_ret_t g_stop_result;
volatile ssi_ret_t g_close_result;

void main(void)
{
    /* Acquire the SSI hardware lock and initialize CH0. */
    g_open_result = R_SSI_Open(TEST_CHANNEL);

    if (SSI_SUCCESS == g_open_result)
    {
        /*
         * -1 is converted to the unsigned underlying type of ssi_mute_t.
         * It becomes an out-of-range value and must return SSI_ERR_PARAM.
         */
        g_mute_minus1_result =
            R_SSI_Mute(TEST_CHANNEL, (ssi_mute_t)-1);

        /*
         * Start SSI before the valid mute ON/OFF tests.
         * An active SSI clock/data connection is needed for Mute ON/OFF
         * to complete without a timeout.
         */
        g_start_result = R_SSI_Start(TEST_CHANNEL);

        if (SSI_SUCCESS == g_start_result)
        {
            /* OnOff = 0: SSI_MUTE_ON; expected SSI_SUCCESS. */
            g_mute_0_result =
                R_SSI_Mute(TEST_CHANNEL, SSI_MUTE_ON);

            /* OnOff = 1: SSI_MUTE_OFF; expected SSI_SUCCESS. */
            g_mute_1_result =
                R_SSI_Mute(TEST_CHANNEL, SSI_MUTE_OFF);
        }

        /*
         * OnOff = 2 is above SSI_MUTE_OFF.
         * The parameter check occurs before any mute operation.
         * Expected: SSI_ERR_PARAM.
         */
        g_mute_2_result =
            R_SSI_Mute(TEST_CHANNEL, (ssi_mute_t)2u);


        if (SSI_SUCCESS == g_start_result)
        {
            g_stop_result = R_SSI_Stop(TEST_CHANNEL);

            if (SSI_SUCCESS == g_stop_result)
            {
                g_close_result = R_SSI_Close(TEST_CHANNEL);
            }
        }
    }
}
```
