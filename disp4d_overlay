% disp4d_overlay_helper(im1,wl1,im2,wl2,cmap,bgnd_bright,gamma_background,gamma_foreground)
%
% overlay(im1,wl1,im2,wl2) - overlays im1 on top of im2
% im1 = first image
% im2 = second image
% wl1 = [black_lev1 white_lev1]
% wl2 = [black_lev2 white_lev2]
%
% overlay(im1,wl1,im2,wl2,cmap)
%   change colormap based on chosen value
%   default is hot
%   cmap = 1: hot
%   cmap = 2: jet
%
% overlay(im1,wl1,im2,wl2,cmap,bgnd_bright)
%   change background brightness
%   bgnd_bright = 0.4 default, range [0,1] 
%
% AZL & JJM
function varargout = disp4d_overlay(varargin)

    if ischar(varargin{1})
        % if first argument passed is a character string
        % invoke a switchyard callback
        try
            if (nargout)
                [varargout{1:nargout}] = feval(varargin{:}); % FEVAL switchyard
            else
                feval(varargin{:}); % FEVAL switchyard
            end
        catch
            disp(lasterr);
        end

    else
        % just overlay an image
        try
            if(nargout)
                [varargout{1:nargout}] = disp4d_overlay_helper(varargin{:});
            else
                disp4d_overlay_helper(varargin{:});
            end
        catch
            disp(lasterr);
        end
    end

function [overlay_colormap] = disp4d_overlay_helper(im1,wl1,im2,wl2,cmap,bgnd_bright,gamma_background,gamma_foreground)

na = nargin;
if(na < 5)
    cmap = 1;
end;

if(na < 6)
    bgnd_bright = 0.4;
end;

if(na < 7)
    gamma_background = 1;
end;

if(na < 8)
    gamma_foreground = 1;
end;

% create transparency data
%
% azl 112910, change this to brighten background
imAlphaData = (1-bgnd_bright)*ones(size(im1));

% create image data struct to pass around
imdata.im1 = im1;
imdata.im2 = im2;
imdata.wl1 = wl1;
imdata.wl2 = wl2;
imdata.imAlphaData = imAlphaData;

iim1 = make_overlay(imdata);

% set up colormap
colormap_full = init_colormap(cmap,gamma_background,gamma_foreground);
colormap( colormap_full );

overlay_colormap = colormap;

hfig = gcf;

handles.imdata = imdata;
guidata(hfig,handles);

set( hfig, 'WindowButtonDownFcn',   'disp4d_overlay(''Press'', gcf, gca)' );
set( hfig, 'WindowButtonUpFcn',     'disp4d_overlay(''Release'', gcf, gca)' );

function colormap_full = init_colormap(cmap,gamma_background,gamma_foreground)
    switch cmap
    case {1,'hot'}
        cmap_background = gray(256);
        cmap_filler = 0*gray(256);
        cmap_foreground = hot(256);
    case {2,'jet'}
        cmap_background = gray(256);
        cmap_filler = 0*gray(256);
        cmap_foreground = jet(256);
    otherwise
        cmap_background = gray(256);
        cmap_filler = 0*gray(256);
        cmap_foreground = hot(256);
    end;
    % apply gamma
    cmap_background = cmap_background.^(gamma_background);
    cmap_foreground = cmap_foreground.^(gamma_foreground);
    colormap_full = [cmap_background; cmap_filler; cmap_foreground];

function iim1 = make_overlay(imdata)
    % display background image
    dil(imdata.im2,imdata.wl2,256,0);
    % overlay im1
    hold on;
    iim1 = dil(imdata.im1,imdata.wl1,256,512);
    set(iim1,'AlphaData',imdata.imAlphaData);
    hold off;

    hfig = gcf;

function varargout = Press(hfig, haxes, varargin)
    set( hfig, 'WindowButtonMotionFcn', 'disp4d_overlay(''Move'', gcf, gca, 0)' );
    disp4d_overlay('Move', gcf, gca, 1);     % Store initial mouse pointer location by calling 'Move' routine
 
function varargout = Release(hfig, haxes, varargin)
    set( hfig, 'WindowButtonMotionFcn', '' );    % This makes the callback a do-nothing operation 

function varargout = Move(hfig, haxes, init, varargin)
    persistent LASTX LASTY MOVED
    ptr = get(hfig,  'CurrentPoint');
    if init == 1
        LASTX = ptr(1);
        LASTY = ptr(2);

    else
        dx = ptr(1)-LASTX;
        dy = ptr(2)-LASTY;

        switch get(hfig,  'SelectionType');

        case 'normal'    % Mouse moved with LEFT button pressed -- change W/L fore
            h = guidata(hfig);

            wl1 = h.imdata.wl1;
            win = wl1(2) - wl1(1);
            lev = (wl1(1)+wl1(2))/2;
            newwin = win * 10^(dx/400);
            newlev = lev - (win*dy)/200;
            
            h.imdata.wl1 = [newlev-newwin/2 newlev+newwin/2];

            make_overlay(h.imdata);
        case 'alt'    % Mouse moved with RIGHT button pressed -- change W/L back
            h = guidata(hfig);

            wl2 = h.imdata.wl2;
            win = wl2(2) - wl2(1);
            lev = (wl2(1)+wl2(2))/2;
            newwin = win * 10^(dx/400);
            newlev = lev - (win*dy)/200;
            
            h.imdata.wl2 = [newlev-newwin/2 newlev+newwin/2];

            make_overlay(h.imdata);
        end
        LASTX = ptr(1);
        LASTY = ptr(2);
    end

function [handle] = dil(im, levels, ngray, offset)
%  
% function di (im, [levels, [[gam], [ngray]]] )
%
% script to display an image
%   - optional levels [min max], otherwise autoscaled from min to max
%   - optional gamma (default 1.0)
%   - optional number of grayscales (default 256)    
%
%

if (~isreal(im))
	disp('Warning: image contains imaginary data not being displayed.');
end;
im = real(im);

% Scale im from 0.0 to 1.0
%
if ( nargin < 2 )
    min_lev = min(im(:));
    max_lev = max(im(:));
else
    if length(levels) ~= 2, 
	error('levels parameter must be 2-element array');
    end;
    
    min_lev = levels(1);
    max_lev = levels(2);
    if min_lev > max_lev, 
	error('levels(1) must be < levels(2)');
    end;
    if ((min(im(:)) < min_lev) || (max(im(:)) > max_lev)), 
	%disp('Warning: Image contains levels outside those displayed');
    end;
end;

if (nargin < 4)
    ngray = 256;
end;

ims = min(1.0, max(0.0, (im - min_lev) / (max_lev - min_lev)));

handle = image (ims * ngray + offset);
axis('off');
axis('image');

hax = gca;

return;

