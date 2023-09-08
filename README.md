Octobotics Embedded Assignment - 1

Write structured C++ Code for Below Tasks , using available libraries.
*Establish Serial Communication with Device at Port = /dev/ttyUSB0 and perform data integrity check using CRC-16.
 (BaudRate::B_115200, NumDataBits::EIGHT, Parity::NONE, NumStopBits::ONE)
*Get Motor Status 2.12 From Document RMD-X Servo Motor Control Protocol v3.6 Documentation


#include <iostream>

#include <boost/asio.hpp>

#include <cstdint>



using namespace boost::asio;



int main() {

	// Define serial port settings (same as in previous code)

	const std::string portName = "/dev/ttyUSB0";

	const int baudRate = 115200;

	const int numDataBits = 8;

	const parity parityOption = parity::none;

	const stop_bits stopBitsOption = stop_bits::one;



	// Initialize IO context and serial port (same as in previous code)

	io_context io;

	serial_port serial(io, portName);



	// Configure serial port settings (same as in previous code)

	serial.set_option(serial_port::baud_rate(baudRate));

	serial.set_option(serial_port::character_size(numDataBits));

	serial.set_option(serial_port::parity(parityOption));

	serial.set_option(serial_port::stop_bits(stopBitsOption));



	// Send the command and null bytes (same as in previous code)



	// Calculate CRC-16 for the command and null bytes (same as in previous code)



	// Send CRC-16 bytes (same as in previous code)



	// Read and process the response

	char responseBuffer[8]; // Assuming a fixed 8-byte response

	size_t bytesRead = read(serial, buffer(responseBuffer, sizeof(responseBuffer)));



	if (bytesRead == sizeof(responseBuffer)) {

		// Interpret the response data according to the provided definitions

		int8_t motorTemperature = static_cast<int8_t>(responseBuffer[1]);

		uint8_t brakeControlCommand = static_cast<uint8_t>(responseBuffer[3]);

		uint16_t voltage = static_cast<uint16_t>((responseBuffer[5] << 8) | responseBuffer[4]);

		uint16_t errorState = static_cast<uint16_t>((responseBuffer[7] << 8) | responseBuffer[6]);



		// Print or use the interpreted data as needed

		std::cout << "Motor Temperature: " << static_cast<int>(motorTemperature) << "℃" << std::endl;

		std::cout << "Brake Control Command: " << static_cast<int>(brakeControlCommand) << std::endl;

		std::cout << "Voltage: " << static_cast<double>(voltage) * 0.1 << "V" << std::endl;

		

		// Check individual error flags using bitwise AND

		if (errorState & 0x0002) {

			std::cout << "Motor Stall detected." << std::endl;

		}

		if (errorState & 0x0004) {

			std::cout << "Low Pressure detected." << std::endl;

		}

		if (errorState & 0x0008) {

			std::cout << "Over Voltage." << std::endl;

		}

		if (errorState & 0x0010) {

			std::cout << "Over Current." << std::endl;

		}

		if (errorState & 0x0040) {

			std::cout << "Power Overrun." << std::endl;

		}

		if (errorState & 0x0100) {

			std::cout << "Speeding." << std::endl;

		}

		// Add more error flag checks as needed for other bits in errorState.

		} else {

		std::cerr << "Error reading from serial port." << std::endl;

	}



	// Close the serial port

	serial.close();



	return 0;

}
