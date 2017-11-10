https://stackoverflow.com/questions/47039150/fourier-descriptor-in-matlab
![](https://i.stack.imgur.com/hLJgU.png)

 crop the top-left corner '6' as the 'sample.png' and I want to detect all the '6' in the test image and mark them according to its detected boundary. However, it turns out like this. I've been stuck here for several days and just could not figure it out. Thank you very much!
 
 
![](https://i.stack.imgur.com/0qYSl.png)

```Matlab
% Read sample image and rgb2gray
im=imread('sample.png');
imBW = rgb2gray(im)<50;
B_sample = bwboundaries(imBW_resize,'noholes');

% Store Sample contour coordinates && organize in complex form
x = B_sample{1,1}(:,2);
y = B_sample{1,1}(:,1);
input = complex (x,y);
F =fft(input);

% Scale, rotation, translation invariant
F_positive = abs(F);
DC = F(1);
scale = abs(F(2));
F_trunc = F_positive(2:11);
F_final = F_trunc/scale;


% Label contour with dilated boundary
BW_test = imread('a2.bmp');
imBW_test = rgb2gray(BW_test)<50;
se = strel ('line',8,180);
closeBW = imdilate(imBW_test,se);
[B,L,N,A] = bwboundaries(closeBW,'noholes');
imshow(BW_test);
hold on;
colors=['b' 'g' 'r' 'c' 'm' 'y'];

 for k=1:length(B)
    x1 = B{k,1}(:,2);
    y1 = B{k,1}(:,1);
    input1 = complex (x1,y1);

F1 =fft(input1);

%Invarient to scale, translation, rotation
F_positive1 = abs(F1);
DC1 = F1(1);
scale1 = abs(F1(2));
F_trunc1 = F_positive1(2:11);
F_final1 = F_trunc1/scale1;

%Calculate euclidean distance
distance = norm(F_final - F_final1);
fprintf('Boundary: %i',k);
disp(distance);

  if distance <= 0.15 
    boundary = B{k};
    cidx = mod(k,length(colors))+1;
    plot(boundary(:,2), boundary(:,1),...
         colors(cidx),'LineWidth',2);

    %randomize text position for better visibility
    rndRow = ceil(length(boundary)/(mod(rand*k,7)+1));
    col = boundary(rndRow,2); row = boundary(rndRow,1);
    h = text(col+1, row-1, num2str(L(row,col)));
    set(h,'Color',colors(cidx),'FontSize',18,'FontWeight','bold');
  end
 end
```

A: Knowing where things go wrong would help in finding the issue. For example, have you tried changing the number of Fourier components that you use? Have you tried changing the threshold? Have you tried changing the distance measure?

