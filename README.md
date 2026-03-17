This repository shows my method for solving a cable-supported structure using force balance and MATLAB. The main aim of this study was to find the unknown cable tensions and to understand how manual equilibrium equations can be solved using a computational approach. By applying force balance at the joint and converting the equations into matrix form, MATLAB can be used to obtain the results quickly and accurately. This work helps in connecting basic engineering mechanics with practical numerical solving techniques.
clc;
clear;
close all;

% USER INPUTS
W = input("Enter Load W (lb): ");          % Load at joint B
theta_deg = input("Enter Angle (degrees): "); 

theta = deg2rad(theta_deg);                % Convert to radians

T_BA_limit = input("Enter allowable limit of cable BA (lb): ");
T_BC_limit = input("Enter allowable limit of cable BC (lb): ");

fprintf('Governing Equilibrium Equations:\n');
fprintf('1) Sum Fx = 0: T_BA - T_BC*cos(theta) = 0\n');
fprintf('2) Sum Fy = 0: T_BC*sin(theta) - W = 0\n\n');

% Matrix form: [A]{T} = {B}
A = [1  -cos(theta);     % From Sum Fx = 0
     0   sin(theta)];    % From Sum Fy = 0

B = [0;
     W];

% Solve for tensions
T = A\B;

T_BA = T(1);   % Horizontal cable
T_BC = T(2);   % Sloped cable

fprintf('\nRESULTS\n');
fprintf('Load W = %.2f lb\n', W);
fprintf('Angle = %.2f degrees\n\n', theta_deg);

fprintf('Tension in cable BA (horizontal) = %.2f lb\n', T_BA);
fprintf('Tension in cable BC (sloped) = %.2f lb\n', T_BC);

%% SMART ALERT SYSTEM

fprintf('\nSAFETY CHECK\n');

if T_BA > T_BA_limit
    warning('ALERT: Cable BA is OVERLOADED!');
else
    disp('Cable BA is within safe limit.');
end

if T_BC > T_BC_limit
    warning('ALERT: Cable BC is OVERLOADED!');
else
    disp('Cable BC is within safe limit.');
end

%% EQUILIBRIUM CHECK

Fx = T_BA - T_BC*cos(theta);
Fy = T_BC*sin(theta) - W;

fprintf('\nEQUILIBRIUM CHECK\n');
fprintf('Sum Fx = %.6f (should be ~0)\n', Fx);
fprintf('Sum Fy = %.6f (should be ~0)\n', Fy);

%% GRAPH: Tension vs Load

W_range = 0:10:200;        % Load values

TBC_vals = W_range / sin(theta);
TBA_vals = TBC_vals .* cos(theta);

figure;
plot(W_range, TBA_vals, 'LineWidth', 2); 
hold on;
plot(W_range, TBC_vals, 'LineWidth', 2);

grid on;
xlabel('Load W (lb)');
ylabel('Cable Tension (lb)');
title('Cable Tension vs Load');
legend('Tension in BA','Tension in BC');
