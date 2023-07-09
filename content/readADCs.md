# Read ADCs

In order to read raw values from the ADCs, use

```
# cat /sys/bus/iio/devices/iio\:device0/in_voltageN_raw
```
Where `N` is the channel number `0 - 5`. In `C` the following can be used:
```
#include <stdio.h>

#define ADC_FILE "/sys/bus/iio/devices/iio:device0/in_voltage0_raw" // file path to ADC result

int main() {
FILE *fp;
int result;

fp = fopen(ADC_FILE, "r"); // open the file in read mode
if (fp == NULL) {
printf("Error opening file!\n");
return 1;
}

fscanf(fp, "%d", &result); // read the ADC result from the file
printf("ADC Result: %d\n", result); // print the ADC result

fclose(fp); // close the file

return 0;
}
```
